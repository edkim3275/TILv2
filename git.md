# Git

## 명령어 정리

### 브랜치 관련

| 명령어                                              | 기능                            | 비고              |
| --------------------------------------------------- | ------------------------------- | ----------------- |
| `git branch`                                        | 로컬에서 이동가능한 브랜치 확인 |                   |
| `git branch -a`                                     | 로컬, 원격 브랜치 목록 확인     | `--all`의 약자    |
| `git branch -r`                                     | 원격 저장소만 확인              | `--remote`의 약자 |
| `git branch <branch_name>`                          | 로컬 브랜치 생성                |                   |
| `git checkout <branch_name>`                        | 해당 브랜치에 체크아웃          |                   |
| `git switch <branch_name>`                          | 해당 브랜치에 체크아웃          |                   |
| `git checkout -b <branch_name>`                     | 브랜치 생성함과 동시에 체크아웃 |                   |
| `git switch -c <branch_name>`                       | 브랜치 생성함과 동시에 체크아웃 |                   |
| `git branch -d <branch_name>`                       | 로컬 브랜치 삭제                | `--delete`의 약자 |
| `git push <branch_name> -d <branch_name>`           | 원격 브랜치 삭제                |                   |
| `git branch --set-upstream-to origin/<branch_name>` | 로컬, 원격 브랜치 연동          |                   |
| `git merge <branch_name>`                           | 브랜치 병합                     |                   |

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

    

    