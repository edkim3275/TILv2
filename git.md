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
| `git branch -d <branch_name>`                          | 로컬 브랜치 삭제                | `--delete`의 약자 |
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

## 브랜치 전략

### git flow

### thunk based