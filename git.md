# Git

## 명령어 정리

### 브랜치 관련

| 명령어                                                 | 기능                            | 비고              |
| ------------------------------------------------------ | ------------------------------- | ----------------- |
| `git branch`                                           | 로컬에서 이동가능한 브랜치 확인 |                   |
| `git branch -a`                                        | 로컬, 원격 브랜치 목록 확인     | `--all`의 약자    |
| `git branch -r`                                        | 원격 저장소만 확인              | `--remote`의 약자 |
| `git branch <branch_name>`                             | 로컬 브랜치 생성                |                   |
| `git checkout <branch_name>`                           | 해당 브랜치에 체크아웃          |                   |
| `git switch <branch_name>`                             | 해당 브랜치에 체크아웃          |                   |
| `git checkout -b <branch_name>`                        | 브랜치 생성함과 동시에 체크아웃 |                   |
| `git switch -c <branch_name>`                          | 브랜치 생성함과 동시에 체크아웃 |                   |
| `git checkout -t remote origin path`                   | 원격 브랜치 가져오기            |                   |
| `git branch -d <branch_name>`                          | 로컬에 있는 브랜치 삭제         | `--delete`의 약자 |
| `git push <remote_branch_name> -d <local_branch_name>` | 원격 브랜치 삭제                |                   |
| `git branch --set-upstream-to origin/<branch_name>`    | 로컬, 원격 브랜치 연동          |                   |
| `git merge <branch_name>`                              | 브랜치 병합                     |                   |

### 리모트 관련

| 명령어                                    | 기능                                         | 비고 |
| ----------------------------------------- | -------------------------------------------- | ---- |
| `git remote -v`                           | 현재 연결되어있는 remote 저장소 url주소 확인 |      |
| `git remote set-url origin "url_address"` | 연결된 remote 저장소 url 주소 변경           |      |

- `git pull ` 하려는 브랜치가 현재 브랜치 커밋과 다른 경우에는 3가지 방식이 존재.

  우선 `git pull origin <가져오려는 branch>` 명령을 실행하고,

  1. 병합(merge) 방식으로 pull 수행 : `git config pull.rebase false`

  2. 리베이스(rebase) 방식으로 pull 수행 : `git config pull.rebase true`

     리베이스는 현재 브랜치의 변경 사항을 다른 브랜치의 최신 커밋 위에 올려놓는 작업. 리베이스를 하게되면 브랜치의 히스토리가 선형으로 유지되며, 커밋들이 깔끔하게 정리될 수 있음. 일반적으로 개발한 내용을 다른 브랜치에 반영할 때 리베이스를 사용

  3. fast-forward만 허용하여 pull 수행 : `git config pull.ff only`

     fast-forward는 현재 브랜치와 가져올 브랜치가 분기되기 이전의 커밋을 공유하고 있을 때 적용할 수 있는 병합방식. 즉, 가져올 브랜치의 변경 내용이 현재 브랜치에 모두 포함되어 있어 별도의 병합 커밋이 필요하지 않을 때 fast-forward 병합이 수행됨.

- 브랜치 병합하기

  > A브랜치의 변경 사항을 B 브랜치에 병합하기

  - 작업사항 커밋하기(A브랜치)

    ```bash
    $ git add .
    $ git commit <commit_msg>
    ```

  - B 브랜치로 이동

    B 브랜치에 A 브랜치를 넣기 위해서는 우선 B브랜치에 'HEAD'를 위치하게 만들어야 함

    ```bash
    $ git checkout <B_branch_name>
    or
    $ git switch <B_branch_name>
    ```

  - B 브랜치에 병합

    ```bash
    $ git merge <A_branch_name>
    ```


- 원격 브랜치 삭제

  ```bash
  로컬 브랜치 삭제
  $ git branch -d <local_branch_name>
  
  원격 브랜치 삭제
  $ git push origin --delete <remote_branch_name>
  ```

- `git fetch`를 하는 경우

  `git pull`과 유사해 보이지만 `git pull` 명령어는 원격 저장소의 업데이트 내용을 가져옴과 동시에 현재 작업하는 로컬 브랜치로 병합.

  `git fetch` 명령어는 원격 저장소의 업데이트를 확인하고 로컬 브랜치에 업데이트된 내용을 다운로드 함. 하지만 병합을 하지는 않음.(원격 저장소와 로컬 저장소간의 다른 내용을 확인할 수 있음)

  따라서 `git fetch`를 실행한 이후에는 `git merge` 혹은 `git rebase`를 사용하여 로컬 브랜치에 업데이트를 병합해야 한다.

  git fetch --all : 모든 원격(remote)들의 새 참조만 가져옴(전체 원격 상태 최신화). 즉, 다른 동료 브랜치들의 최신 커밋 포인터를 로컬에 반영. 

- `git merge` vs. `git rebase`

  merge는 브랜치를 통합하는 것. rebase는 브랜치의 base를 옮기는 것.

  두 가지 경우가 존재

  1. merge만 사용한다.
  2. rebase와 merge를 사용한다.

  - rebase

    말그대로 're' + 'base' base를 재설정한다는 의미. 여기서 base는 브랜치의 base를 의미하는데, 브랜치는 base 지점을 갖고 있어서 base에서부터 코드를 수정하게 됨.

    git history가 선형으로 깔끔해지면서 협업 시 merge만 했을 경우보다 history 확인이 상대적으로 명확해진다는 장점

- `git config credential.helper store`

  git push/pull 시에 유저정보 기입하는 중복 작업 생략

- `git pull origin develop` => `git pull --set-upstream develop`

  => `git pull` === `git pull origin develop`

## 개념 정리

### HEAD

- 현재 체크아웃 한 커밋(또는 브랜치)을 가리키는 포인터
- 브랜치 이동 시 HEAD가 그 브랜치의 최신 커밋을 가리키도록 옮겨진다.

### 커밋(commit)

- 스냅샷 + 메타데이터(작성자/메시지/시간) + 부모 포인터를 가진 불변 노드
- 각 커밋은 해시로 식별되고, 부모를 따라가며 히스토리가 만들어짐

### 브랜치(branch)

- 어느 커밋을 가리키는 포인터. 즉, 새 브랜치를 만들면 현재 커밋을 가리키는 새 이름이 생김
- 원격 추적 브랜치(remote-tracking branch)란?
  - 원격 저장소의 특정 브랜치가 현재 어느 커밋을 가리키는지 로컬에 복제해 둔 읽기 전용 포인터
  - 예를들어 `remotes/origin/develop`이라고 한다면 원격 저장소의 develop이 가리키는 커밋 해시를 추적하는 포인터.
  - 원격 추적 브랜치는 자동으로 갱신되지 않고 git fetch 혹은 git pull을 할 때 갱신된다.
  - git branch -d <branch_name> 하고 git branch -a를 해도 원격 브랜치가 확인되는 이유는 git branch -d 가 로컬 브랜치를 삭제하는 명령어이기도하고, 원격 추적 브랜치는 건드리지 않는다. 따라서 `git push origin --delete feature/#1` 과 같이 원격 브랜치도 같이 삭제해준다. 혹은 원격에서 이미 삭제된 경우 `git fetch --prune` or `git fetch -p`를 통해 캐시 정리만 해준다.

### 태그

- 특정 커밋에 이름표(라벨)을 붙여서 "이 시점이 중요하다"를 표시하는 기능

- 주로 릴리스 버전관리에 사용

  ```
  git tag v1.0.0
  git push origin v1.0.0
  ```

  v1.0.0이라는 이름이 현재 커밋에 달리고, 다른 사람도 git checkout v1.0.0으로 그 시점 코드를 그대로 볼 수 있음.

### merge / rebase

- merge : 두 줄기를 통합 커밋 하나로 합치기
- rebase : 한 줄기의 커밋을 다른 기준 위로 "재작성"

## 브랜치 전략

### git flow

- Vincent Driessen의 모델을 기반으로 한 표준화 된 협업 전략

| 브랜치    | 용도                              | 생성 기준                                          |
| --------- | --------------------------------- | -------------------------------------------------- |
| main      | 실제 서비스 운영 코드(production) | 오직 릴리스 버전만 merge                           |
| develop   | 다음 버전을 준비하는 통합 브랜치  | feature -> develop                                 |
| feature/* | 단위 기능 개발                    | develop에서 분기, 작업 후 develop 으로 merge       |
| release/* | 배포 직전 QA 및 버그 수정         | develop에서 분기, QA 후 main과 develop에 merge     |
| hotfix/*  | 운영 중 긴급 수정                 | main에서 바로 분기, 수정 후 main과 develop에 merge |

```
main
└── hotfix/1.0.1
develop
 ├── feature/login-#1
 ├── feature/signup-#2
 └── release/1.1.0
```

- 프로젝트 규모나 팀 구성에 따라 단순화해서 사용하기도 함

- PR 절차에서의 base

  - PR 절차에서 base는 목표(받는) 브랜치를 의미, head(=compare)는 PR의 소스 브랜치를 의미

  - 일반적으로 기능 개발(feature/)은 develop으로 통합되므로

    base = develop, compare(head) = feature/* 로 설정 => "이 기능을 develop에 합친다."

- PR(Pull Request) 예시

  ```
  ## 개요
  - PR의 목적/배경을 간단히 설명해주세요.
  
  ## 변경 내용
  - [ ] 주요 기능 A 추가
  - [ ] 컴포넌트 B 리팩터링
  - [ ] 스타일/문구 수정 등
  
  ## 스크린샷/동작 데모(선택)
  - 전/후 비교 이미지 또는 GIF
  
  ## 테스트
  - [ ] 유닛/통합 테스트 추가 혹은 업데이트
  - [ ] 로컬에서 주요 케이스 수동 점검 완료
  
  ## 체크리스트
  - [ ] 문서/주석 업데이트
  ```

### thunk based

## Gitea

- Gitea레포 작업 내용을 개인 레포에도 적용하고 싶은경우

  1. 기존 origin 을 남기고 github 원격 추가

     ```
     git remote add github https://github.com/edgar-kim/project.git
     ```

  2. 전체 브랜치/태그 포함해서 github 푸시

     ```
     git push --mirror github
     ```

  3. (작업 후) gitea와 github 둘 다 동시에 push

     ```
     git push origin develop
     git push github develop
     ```

  4. 추후에 github으로 완전히 전환하고 싶다면

     ```
     git remote remove origin
     git remote rename github origin
     ```

- 혹은 임시로 origin이 둘 다 되게 설정하는 방법도 존재

