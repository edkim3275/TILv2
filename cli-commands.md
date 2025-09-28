# 명령어 정리

## Docker

| **명령어**                    | **설명**                             |
| ----------------------------- | ------------------------------------ |
| docker-compose up -d          | 백그라운드 실행                      |
| docker-compose up -d --build  | 새로 빌드해서 실행(최신코드 반영 시) |
| docker-compose down           | 컨테이너 종료 및 정리                |
| docker-compose logs -f        | 로그 실시간 보기                     |
| docker ps                     | 실행 중인 컨테이너 확인              |
| docker-compose exec blog bash | 블로그 컨테이너 안으로 진입          |

## Prisma

| **명령어**               | **설명**                                           | **실행 시기**                          |
| ------------------------ | -------------------------------------------------- | -------------------------------------- |
| npx prisma generate      | Prisma Client 생성                                 | schema.prisma 수정 후                  |
| npx prisma db push       | schema.prisma를 DB에 직접 반영 (마이그레이션 없이) | 빠르게 테스트할 때                     |
| npx prisma migrate dev   | 마이그레이션 생성 & 적용                           | 정식 구조 반영할 때                    |
| npx prisma migrate reset | DB 초기화 → 마이그레이션 적용 → seed 실행          | 초기 세팅 or 재설정할 때               |
| npx prisma db seed       | seed 파일(prisma/seed.ts) 실행                     | 마이그레이션 후, 수동으로 seed 원할 때 |

## SSH

## Mac OS

| 명령어                       | 설명                      |
| ---------------------------- | ------------------------- |
| `netstat -nr | grep default` | 맥북 게이트웨이 주소 확인 |
| `curl https://ifconfig.me`   | 외부 IP 주소 확인         |
- `netstat -nr | grep default`는 우리가 인터넷에 나갈 때 거치는 첫 번째 기기 = 게이트웨이(보통 공유기)의 IP를 보여준다.

## Ubuntu

| 명령어                                                       | 설명                                    |
| ------------------------------------------------------------ | --------------------------------------- |
| `sudo lsof -i tcp:80`                                        | 현재 80 포트를 사용중인 프로세스들 확인 |
| `hostname -I`                                                | 내부 IP 주소 확인                       |
| `nmcli device wifi list`                                     | 주변 wifi 검색                          |
| `nmcli device wifi connect "공유기이름" password "비밀번호"` | 연결시도                                |

## Nginx

Nginx 켜기

```bash
sudo systemctl start nginx
```

Nginx 멈추기

```bash
sudo systemctl stop nginx
```

> Nginx의 포트 사용을 중단하고, 초기 상태처럼 없애기
