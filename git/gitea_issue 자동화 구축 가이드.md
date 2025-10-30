# Gitea Dialy Log 자동화 설정 관리

>팀원별 Daily Log 이슈를 평일 오전 8:30(KST)에 자동 생성해 매일 개발 진행 현황을 손쉽게 기록하고 관리하기 위한 자동화 시스템을 구축한다.

- 프로젝트를 진행하면서 각 팀원별로 데일리로 작업한 내용을 확인하기 위해서 Gitea에 Daily Log 이슈를 작성하기로 함
- 해당 이슈를 템플릿을 적용하여 매일 아침 생성
- Gitea Actions + self-hosted Runner(OCI 서버에서 실행)
- develop 브랜치에서 .gitea/ 설정을 관리

## 준비단계

### 1. Gitea Personal Access Token 발급

1. **Settings → Applications → Manage Access Tokens**
2. Scopes
   - All (public, private, and limited)
   - issue, repository, user → Read and Write

3. 발급 된 엑세스 토큰 저장 (한 번만 표시되니 다른 곳에 저장 필수!)

### 2. Gitea Secrets 등록

1. **Repo Settings → Actions → Secrets**
2. 토큰의 이름을 설정하고 Access Token 값 등록

### 3. 프로젝트 구조

```
.gitea/
 ├─ ISSUE_TEMPLATE/
 │   └─ daily-log.md
 └─ workflows/
     └─ daily-log.yml
```

- daily-log.md 에는 이슈로 작성될 템플릿 작성

- daily-log.yml에 gitea actions를 정의

  ```yaml
  name: create-daily-log
  
  on:
    schedule:
      # KST 08:30 (Mon-Fri) == UTC 23:30 (Sun-Thu)
      - cron: "30 23 * * 0-4"
    workflow_dispatch:
  
  jobs:
    create:
      runs-on: [self-hosted, Linux, X64]
      steps:
        - name: Ensure jq
          run: |
            if ! command -v jq >/dev/null 2>&1; then
              sudo apt-get update -y && sudo apt-get install -y jq
            fi
  
        - name: Create Daily Log issue from template
          env:
            GITEA_BASE: https://your.gitea.host
            REPO: your-org/your-repo
            TOKEN: ${{ secrets.DAILY_LOG_TOKEN }}
          run: |
            set -e
            TODAY_KST=$(TZ=Asia/Seoul date +%m.%d)
            TITLE="${TODAY_KST} Daily Log"
  
            # 1) prevent duplicate issue (URL-encode the title)
            Q=$(python3 -c "import urllib.parse,sys; print(urllib.parse.quote(sys.argv[1]))" "$TITLE")
            EXIST=$(curl -s -H "Authorization: token $TOKEN" \
              "$GITEA_BASE/api/v1/repos/$REPO/issues/search?q=${Q}&limit=1" \
              | jq -r '.total_count // .issues|length // 0')
            if [ "$EXIST" != "0" ]; then
              echo "Already exists: $TITLE"
              exit 0
            fi
  
            # 2) get raw template from repository (adjust ref if default branch changes)
            RAW_URL="$GITEA_BASE/api/v1/repos/$REPO/raw/.gitea/ISSUE_TEMPLATE/daily-log.md?ref=develop"
            TEMPLATE=$(curl -fsSL "$RAW_URL")
  
            # 3) replace {{date}} placeholder
            BODY="${TEMPLATE//\{\{date\}\}/$TODAY_KST}"
  
            # 4) build JSON payload
            PAYLOAD=$(jq -n \
              --arg title "$TITLE" \
              --arg body "$BODY" \
              --argjson labels '["type:daily-log"]' \
              --argjson assignees '["edgar_kim","ahnseoyeon","sojy001","zorba"]' \
              '{title:$title, body:$body, labels:$labels, assignees:$assignees}')
  
            # 5) create issue
            curl -s -X POST "$GITEA_BASE/api/v1/repos/$REPO/issues" \
              -H "Authorization: token $TOKEN" \
              -H "Content-Type: application/json" \
              -d "$PAYLOAD" \
              | jq -r '.number // "created"'
              --argjson assignees '["edgar_kim","ahnseoyeon","sojy001","zorba"]' \
              '{title:$title, body:$body, labels:$labels, assignees:$assignees}')
  
            # 5) 이슈 생성
            curl -s -X POST "$GITEA_BASE/api/v1/repos/$REPO/issues" \
              -H "Authorization: token $TOKEN" \
              -H "Content-Type: application/json" \
              -d "$PAYLOAD" \
              | jq -r '.number // "created"'
  ```

### 4. Self-hosted Runner 설치(OCI 서버)

- 설치 경로 준비

  ```bash
  sudo mkdir -p /var/lib/gitea-runner
  cd /var/lib/gitea-runner
  ```

- 러너 바이너리 설치

  ```bash
  sudo curl -fsSL https://dl.gitea.com/act_runner/0.2.13/act_runner-0.2.13-linux-amd64 \
    -o /usr/local/bin/act_runner
  sudo chmod +x /usr/local/bin/act_runner
  ```

- 러너등록

  ```bash
  sudo -u ubuntu bash -lc '
  cd /var/lib/gitea-runner && \
  /usr/local/bin/act_runner register \
    --instance "https://your.gitea.host" \
    --token "<Registration_Token>" \
    --name "oci-runner" \
    --labels "self-hosted,Linux,X64" \
    --no-interactive
  '
  ```

### 5. 서비스로 등록(자동 실행)

```bash
sudo tee /etc/systemd/system/gitea-runner.service >/dev/null <<'EOF'
[Unit]
Description=Gitea Actions Runner (OCI)
After=network.target

[Service]
ExecStart=/usr/local/bin/act_runner daemon
WorkingDirectory=/var/lib/gitea-runner
User=ubuntu
Restart=always
RestartSec=5

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable gitea-runner
sudo systemctl start gitea-runner
sudo systemctl status gitea-runner
```

🟢 Active: active (running) 이면 정상 작동 중

### 6. 정상 동작 확인

1. **Gitea → Settings → Actions → Runners**

   → oci-runner가 🟢 Online 인지 확인

2. **Actions → daily-log.yml → Run workflow**

   → 수동 실행 후 **Issues 탭**에 MM.DD Daily Log 생성 확인

3. 자동 실행은 매일 평일 오전 8:30에 작동

## 프로젝트 종료 후 처리 방법

### **📴 자동 생성 중단**

1. .gitea/workflows/daily-log.yml 이름을 변경

   (예: _daily-log.yml.bak) → 자동 실행 중단

2. 또는 on.schedule 블록을 주석 처리 (# 붙이기)

### **🧰 러너 해제 및 정리**

```bash
sudo systemctl stop gitea-runner
sudo systemctl disable gitea-runner
sudo rm /etc/systemd/system/gitea-runner.service
sudo rm -rf /var/lib/gitea-runner
sudo rm /usr/local/bin/act_runner
sudo systemctl daemon-reload
```

| 목적             | 명령어                              |
| ---------------- | ----------------------------------- |
| 서비스 상태 확인 | sudo systemctl status gitea-runner  |
| 로그 확인        | journalctl -u gitea-runner -f       |
| 강제 재시작      | sudo systemctl restart gitea-runner |
| 중단             | sudo systemctl stop gitea-runner    |
| 자동 시작 해제   | sudo systemctl disable gitea-runner |

### :scissors: 완전 삭제

```
sudo rm -rf /var/lib/gitea-runner /usr/local/bin/act_runner /etc/systemd/system/gitea-runner.service
```

## 개념 요약

- Gitea actions : Github actions처럼 레포 내부에 정의된 YAML 워크 플로우를 실행하는 자동화 플랫폼
- Personal Access Token(PAT) : Gitea API 호출 시 인증하기 위한 키(자동화의 신원증명 수단)
- Repository Secret : 민감정보를 안전하게 레포 내부에서 참조하도록 저장하는 비밀 저장소
- Self-hosted Runner : 실제로 YAML 워크 플로우를 실행하는 서버(나는 OCI 인스턴스로 진행)
- Systemed 서비스 등록 : 러너를 서버 재부팅 후에도 항상 실행 상태로 유지시키는 자동 구동 설정
- 프로젝트 구조
  - `.gitea/ISSUE_TEMPLATE/daily-log.md` : Gitea에서 이슈를 생성할 때 사용할 기본 양식 정의
  - `.gitea/workflows/daily-log.yaml` : Gitea Actions가 인식하는 자동화 스크립트인 YAML 파일을 저장하는 폴더
- Personal Access Token의 필요성
  - 워크플로우(YAML)는 내부적으로 Gitea REST API를 호출해서 이슈를 생성(POST "$GITEA_BASE/api/v1/repos/$REPO/issues)하기 때문에 해당 API를 사용하려면 사용자 본인의 인증 토큰인 PAT이 필요함.

- Git Secrets 등록
  - 워크플로우 파일은 공개 저장소 내부에 존재하기 때문에 토큰을 하드코딩하면 보안상 매우 위험
  - 따라서 Gitea는 "Actions Secrets" 기능을 제공
  - 즉 민감한 값(PAT)를 저장하고 워크플로우에서는 `${{ secrets.DAILY_LOG_TOKEN }}` 형태로 참조
- Self-hosted Runner 설치
  - Gitea Actions는 워크플로우를 실행할 실제 컴퓨팅 환경이 필요(Github은 클라우드 러너를 제공하지만 Gitea는 직접 러너를 운영해야함)
  - 따라서 OCI 인스턴스 위에 act_runner를 설치해 "실행기" 역할을 맡긴 것
- 서비스 등록(systemd)
  - 리눅스 서버는 재부팅 시 모든 프로세스가 종료되므로 러너를 서비스로 등록해야 서버가 다시 켜질 때 자동으로 실행된다.