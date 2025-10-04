# 라즈베리파이 설치

## 라즈베리파이 사양

- Raspberry Pi 5 8GB

  - CAO24Y10010T9

  - 64-bit quad-core Cortex-A76 processor

    8GB LPDDR4X SDRAM

  - 2 micro HDMI ports

    2 USB 3.0 ports

    2 USB 2.0 ports

    2 4-lane connectors for camera or display peripherals

    Gigabit Ethernet port

    Bluetooth 5.0

## 운영체제 설치

- Raspberry Pi Imager 설치

  https://www.raspberrypi.com/software/

  ![image-20250407133528689](/Users/edgar/Library/Application Support/typora-user-images/image-20250407133528689.png)

  ![image-20250407133651191](/Users/edgar/Library/Application Support/typora-user-images/image-20250407133651191.png)

  PC에서 SSH로 라즈베리파이를 연결해 작업을 처리해야한다.

  1. 라즈베리파이를 wifi와 연결한다.
  2. 공유기를 통해 라즈베리파이에 할당된 주소를 찾는다.
  3. PC에서 라즈베리파이에 할당된 주소로 SSH 요청을 보내 접근한다.

  - 사용자 정보 : 라즈베리파이 유저의 이름 & 비밀번호. 추후 SSH 접속 시 이용된다.

  - 무선 LAN : 라즈베리파이가 기본으로 연결할 wifi 주소와 비밀번호

  - 위의 두 정보를 입력하고 SSH 사용을 활성화한 후, OS를 만들어 라즈베리파이에 연결한다. 라즈베리파이에 전원을 넣으면 입력한 무선 LAN에 자동으로 연결되며, SSH로 접속할 수 있는 상태가 된다.

    SK_WiFiGIGA3D84_5G, 2011033174

  ![image-20250407135329166](/Users/edgar/Documents/TILv2/라즈베리파이설치(MAC).assets/image-20250407135329166.png)

  ![image-20250407135340827](/Users/edgar/Documents/TILv2/라즈베리파이설치(MAC).assets/image-20250407135340827.png)

  - Key-gen

    ```
    ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQCZs6N64gmB8pxKf4Y8kNL74VyVFYu5tCzIY40nv3M98iuTfDjCiCFa8arX7Mcrmqw4dZSuc026C6r07P0Pu6VEf+W/jSRL/zNo7QPjGhfSMsNTiOB3OFot+cR4hARAQU9w0VNwECWfRnfTUDG3igTtCP/YF5ADkzXL4IQSgSACz+4InhliKbeF5jVSZHmEqlDEuBoqVvv83+tLyKJxvs9nLFb3JEdgecwFn/J1GlBqUSdQw7irmvl2TyJjag2G+6VWYlMWPqe6gzpcp9E4lP2mq30xtRli7ucVTzqVMg8KcBc/mfCJo70krSU1tUU2sX+wK96RnnASWwsaribhjibj/NrF32RR/kpfyrXnhme5M+l/Va5IaY3GP5jjMVj9Dr756QU508p6TzU87Rp7ulfpTFYlAIpwJHfvQWHSXZNo07yv3jacVx/xquoyI3HKEEV6bu19oQAA0MFqqTL5Zxxnf+qENL9zTqoLu0xM+E+Pi8WVdpYCCrUJbTmJSCRSYT0= edgar@MENGs-Laptop.local
    ```

  - 시큐어 셸(SSH)

    SSH(Secure Shell)는 네트워크를 통해 안전하게 컴퓨터 시스템에 접근할 수 있도록 해주는 암호화 프로토콜이자 프로그램 이름. 라즈베리파이 OS를 설치할 때 커스터마이징 OS 설정에서 SSH를 활성화해야 원격으로 접속할 수 있게 문을 만들어 둔 것.

    - 비밀번호 인증

      사용자명과 비밀번호를 이용하여 원격지에 접속하는 방식

    - 공개키 인증

      공개키와 개인키를 사용하여 인증하는 방식

      공개키는 배포할 수 있고, 개인키는 보호해야 한다.

      SSH키를 이용하면 GitLab 원격 서버에 인증할 때마다 사용자 이름과 비밀번호를 입력할 필요가 없다.

      SSH 공개키를 이용하면 편의성과 보안성 모두를 높일 수 있다.

  ![image-20250407135353642](/Users/edgar/Documents/TILv2/라즈베리파이설치(MAC).assets/image-20250407135353642.png)

  ![image-20250407141320099](/Users/edgar/Documents/TILv2/라즈베리파이설치(MAC).assets/image-20250407141320099.png)

  ![image-20250407141422822](/Users/edgar/Documents/TILv2/라즈베리파이설치(MAC).assets/image-20250407141422822.png)

  ![image-20250407141837465](/Users/edgar/Documents/TILv2/라즈베리파이설치(MAC).assets/image-20250407141837465.png)

- 네트워크 설정하기

  - SD카드의 /boofs 폴더에

    1. 빈 ssh 파일을 생성.

       라즈베리파이는 보안을 위해 기본적으로 SSH가 꺼져있음. 따라서 SSH를 키기위해 라즈베리는 다음 규칙을 따른다. 부팅할 때 boot 파티션에 ssh라는 빈 파일이 있으면 부팅하면서 자동으로 ssh 서버를 활성화해줌

- 맥 OS에서 터미널 프로그램으로 SSH 접속

  - 맥OS에서 터미널 프로그램을 열어 다음과 같은 형식으로 명령어를 입력한다. 먼저 ssh라는 명령어를 입력하고, 사용자명과 라즈베리파이 이름을 차례로 입력한다. 사용자명과 라즈베리파이 이름은 @ 문자로 구분한다. 여기에서 사용자명과 라즈베리파이 이름은 라즈베리파이 OS를 설치할 때 커스터마이징 OS에서 입력한 사용자명과 라즈베리파이 이름을 사용해야한다. 만약 커스터마이징 OS를 적용하지 않고 기본 설정으로 사용한 경우 사용자명은 pi이고, 라즈베리파이의 이름은 raspberrypi.local이다. 또한 접속시 필요한 비밀번호는 raspberry이다. 라즈베리파이 이름을 다른 용어로 호스트명이라고 부른다. 호스트는 네트워크에 연결된 장치로서, 데이터를 전송하거나 수신할 수 있는 컴퓨터나 기타 장치를 의미한다.

    나의 경우 `ssh edgar@raspberrypi.local`이 된다.