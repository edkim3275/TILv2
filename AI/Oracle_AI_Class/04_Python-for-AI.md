# DAY5

- SSH를 통한 서버 접속 간소화

  -  ssh-key 경로가 너무 깊으면 서버에 접속 할 때마다 `ssh -i ~/Documents/Oracle/.../ssh-key.key ubuntu@...` 와 같이 작성을 해줘야하기에 번거롭다. ssh 접속 시 미리 key의 경로, Host 주소, 사용자명 등 원격 접속에 필요한 정보들을 미리 기입해두고 정해놓은 닉네임으로 손 쉽게 접근하는 방법이 있다.

  - `mv 기존경로/ssh-key.key ~/.ssh/oci-key.pem` : ssh를 ~/.ssh/oci-key.pem 으로 이동

  - SSH 설정파일(~/.ssh/config)을 사용하여 ssh-key 경로나 IP 등 정보를 설정

    ```bash
    nano ~/.ssh/config
    ```

    ```bash
    Host oci-server
        HostName 130.162.146.139
        User ubuntu
        Indentify ~/.ssh/oci-key.pem
    ```

  - 이제 서버에 `ssh oci-server` 이렇게 단순히 접속 가능

## AI를 위한 Python

### 환경설정

- $ python -m venv 가상환경이름 : 가상환경 생성

- $ source 가상환경이름/bin/activate : 가상환경 활성화

- (가상환경 활성화 상태에서) $ deactivate : 가상환경 비활성화

- 가상환경으로 venv말고 anaconda 사용시

  $ sudo apt install curl -y : curl 설치

  $ curl --output anaconda.sh https://repo.anaconda.com/archive/Anaconda3-2024.10-1-Linux-x86_64.sh : 아나콘다 레포에 접근해서 설치 스크립트 다운로드

  $ bash anaconda.sh : anaconda.sh 실행. 만약 anaconda.sh를 해서 실행이 안될 경우 bash를 붙여서 실행

  license terms yes로 확인해주고, /home/edu004/anaconda3에 설치한다.

  :bulb: 리눅스 파일 권한(file permission) 644(나만 읽기/쓰기 가능, 다른 사람은 읽기만), 755(나는 읽기/쓰기/실행 가능, 다른 사람은 읽거나 실행만 가능), 777 처럼 전부 여는 경우는 잘 없다.

- 가상 환경에서 진행하게 되면 메인 파이썬 환경이 아니라 가상환경의 파이썬 환경에 설치되는 것

=> 작업을 위한 python 가상환경 설정이 안료됐다! :raised_hands:

### 파이썬 기초

#### 기본 문법

- Java와 비교

  세미콜론 없음. 들여쓰기(indent) 주의! 들여쓰기를 이용하여 범위지정, 동적타이핑, 간결한 코드, 인터프리터 언어

- 비교연산자 is, is not(객체 식별)

- 멤버십연산자 in, not in

- 비트연산자 &, |, ^, ~, <<, >>

  ^(XOR) : 다르면 1, 같으면 0

  ~(NOT) : 비트반전. 파이썬은 정수를 2의 보수 규칙으로 처리힌다.

### 문자열

- strip() : 문자열 양 끝에 있는 공백이나 특정 문자 제거

- join() : 리스트(또는 이터러블)의 문자열들을 특정 구분자로 합칠 때 사용

  ```python
  words = ['hello', 'python', 'world']
  result = ''.join(words)
  ```

- replace() : 특정 부분 문자열을 다른 문자열로 변경

  ```python
  text = "I love Java"
  print(text.replace("Java", "JavaScript")) # I love JavaScript
  
  "aaa".replace("a", "b", 2) # bba
  ```

- find() : 문자열에서 부분 분자열을 찾고, 시작 위치 인덱스 반환

  ```python
  text = "Hello Python"
  print(text.find("Python")) # 6
  print(text.find("Java")) # -1(없으면 -1 반환)
  ```

- split()  : 문자열을 구분자 기준으로 나누어 리스트로 반환

  ```python
  text = "apple,banana,cherry"
  print(text.split(",")) # ["apple", "banana", 'cherry']
  ```

#### 제어구조

- 조건문

  ```python
  # if-elif-else
  score = 85
  if score >= 90:
    print("A")
  elif score >= 80:
    print("B")
  else:
    print("C")
    
  # match-case : if문보다 읽기가 쉬움
  status - 404
  match status:
    case 200:
      print("OK")
    case 404:
      print("Not Found")
    case _:
      print("Unknown status")
  ```

- 반복문

  ```python
  # for-else
  for i in range(5): # 0~4
    print(i)
  
  # 리스트 순회
  fruits = ["apple", "banana", "orange"]
  for fruit in fruits:
    print(fruit)
    
  # while
  count = 0
  while count < 5:
    print(count)
    count += 1
  ```

- 리스트 컴프리헨션

  ```python
  squares = [x**2 for x in range(10) if x % 2 == 0]
  print(squares) # [0, 4, 16, 36, 64]
  ```

- enumerate

  반복문 돌릴 때 반복 대상의 idx와 value를 동시에 얻을 수 있게 해주는 내장함수. enumerate()는 (index, value) 형태의 튜플을 반환하는 이터레이터를 만들어준다.

  enumerate(iterable, start=0)

  iterable : 리스트, 튜플, 문자열 등 순회 가능한 객체

  start : 인덱스를 시작할 값(기본값 0)

  ```python
  fruits = ["apple", "banana", "orange"]
  for idx, fruit in enumerate(fruits, start=1):
    print(f"{idx}. {fruit}")
  # enumerate(fruits, start=1) => <enumerate object at 0x7f10ee898040>
  # list(enumerate(fruits, start=1)) => [(1, 'apple'), (2, 'banana'), (3, 'orange')]
  ```

  ```python
  # list 컴프리헨션과 사용할 경우
  fruits = ["apple", "banana", "orange"]
  fruit_dict = {index: fruit for index, fruit in enumerate(fruits, start=1)}
  print(fruit_dict) # {1: 'apple', 2: 'banana', 3: 'orange'}
  ```

#### 자료구조

- 리스트

  용도 : 생성, 접근, 수정, 슬라이싱

  주요 메서드 : append(값), insert(넣을 위치(인덱스), 값), remove(지우고자하는 요소), pop()

  ```python
  numbers = [1, 2, 3, 4]
  numbers.append(5) # [1, 2, 3, 4, 5]
  numbers.insert(2, 10) # [1, 2, 10, 3, 4, 5]
  numbers.remove(10) # [1, 2, 3, 4, 5]
  last = numbers.pop() # last = 6, numbers = [1, 2, 3, 4]
  del numbers[0] # [2, 3, 4]
  del numbers[0:2] # [4]
  del numbers # 리스트 삭제
  
  # 슬라이싱 list[start:end:step]
  print(numbers[1:3]) # [1, 2]
  ```

  슬라이싱

  ```python
  nums = [1, 2, 3, 4, 5]
  print(nums[0:3]) # [1, 2, 3] 0~2까지
  print(nums[:3]) # [1, 2, 3] 앞에서 3개
  print(nums[2:]) # [3, 4, 5] 인덱스 2~끝까지
  print(nums[:]) # [1, 2, 3, 4, 5] 전체복사
  
  # step 활용 (-1이면 역순. start에서 왼쪽으로 진행)
  print(nums[::2]) # [1, 3, 5] 인덱스 0, 2, 4
  print(nums[1::2]) # [2, 4] 홀수 인덱스만
  print(nums[::-1]) # [5, 4, 3, 2, 1] 역순
  
  # 음수는 뒤부터
  print(nums[-1]) # 5
  print(nums[-2:]) # [4, 5]
  print(nums[:-2]) # [1, 2, 3]
  print(nums[::-1]) # [5, 4 ,3, 2, 1]
  
  # 수정/삭제
  nums[1:3] = [20, 30] # 일부 수정
  print(nums) # [1, 20, 30, 4, 5]
  
  del nums[::2] # 일부 삭제
  print(nums) # [20, 4]
  ```

- 튜플(불변 리스트)

  ```python
  point = (10, 20)
  point.append(20) # 추가 안됨
  x, y = point # 언패킹(list도 가능)
  ```

- 집합(중복 불허)

  ```python
  a = {1, 2 ,3}
  b = {3, 4, 5}
  print(a|b) # 합집합 {1, 2, 3, 4, 5}
  print(a&b) # 교집합 {3}
  a.add(4) # {1, 2, 3, 4}
  # update(iterable)
  b.update([6, 7]) # {3, 4, 5, 6, 7}
  b.update({8, 9}) # {3, 4, 5, 6, 7, 8, 9}
  b.update("ab") # {3, 4, 5, 6, 7, 8, 9, "a", "b"}
  ```

- 딕셔너리

  ```python
  student = {"name": "edgar", "age": 21, "major": "architecture"}
  # 접근
  print(student["name"]) # edgar
  print(student.get("age")) # 21 기본적으로 이걸 사용
  # 추가/수정
  if student.get("grade") == None:
  	student["grade"]="A" # 추가
  student["age"] = 28 # 수정
  # 키 순회
  for key in student:
    print(f"{key}:{student[key]})
  ```

- 얕은 복사 vs. 깊은 복사

  ```python
  # 얕은 복사 시 발생하는 문제
  original = [[1, 2], [3, 4]]
  copied = original.copy()
  copied[0][0] = 99
  print(original) # [[99, 2], [3, 4]]
  
  # 깊은 복사
  import copy
  original = [[1, 2], [3, 4]]
  deep_copied = copy.deepcopy(original)
  deep_copied[0][0] = 99
  print(original) # [[1, 2], [3, 4]]
  ```

  매번 deep copy를 하게되면 메모리 낭비가 너무 심해짐

#### 함수

- 함수 정의 (def)

  ```python
  # greeting
  def greet(name, message="안녕하세요"):
    return f("{message}, {name}님!")
  
  print(greet("철수")) # 안녕하세요, 철수님!
  print(greet("영희", "반갑습니다")) # 반갑습니다, 영희님!
  
  # 계산기
  d = 0 # global 변수
  def add(a, b): # a, b는 argument
    c = a + b # c는 local 변수
    return c
  ```

- 가변인자 : 인자 몇개 받을 지 알 수 없는 경우

  ```python
  def total_sum(*args):
    return sum(args)
  print(total_sum(1, 2, 3, 4, 5))
  ```

#### File

- 파일쓰기

  ```python
  with open("example.txt", "w", encoding="utf-8") as f:
    f.write("Python 파일 입출력 테스트\n")
  ```

  encoding="utf-8" : 한글도 같이 처리하기 위함

- 파일읽기

  ```python
  with open("example.txt", "r", encoding="utf-8") as f:
    content = f.read()
    print(content)
  ```

- JSON예제

  ```python
  import json
  
  data = {"name": "Edgar", "age": 28}
  # 쓰기
  with open("data.json", "w") as f:
    json.dump(data, f)
    
  # 읽기
  with open("data.json", "r") as f:
    loaded = json.load(f)
    print(loaded) # {"name": "Edgar", "age": 28}
  ```

  data.json : 상대경로로써 현재 스크립트를 실행한 작업 디렉토리에 파일이 만들어진다. 절대경로를 적으면 지정한 위치에 파일이 저장된다. 예를들어 with open("/home/edu004/temp", "w") as f: ... 으로 하면 해당 위치에 저장된다.

  파이썬에서 현재 작업 디렉토리 확인

  ```python
  import os
  print(os.getcwd()) # 현재 작업 디렉토리 경로 출력
  ```

#### 객체

- 객체 선언 및 접근

  ```python
  class Student:
    def __init__(self, name):
      self.name = name
      self.scores = []
      
    def add_score(self, score):
      self.scores.append(score)
      
    def average(self): # 클래스 내의 함수는 '메소드'
      return sum(self.scores) / len(self.scores)
  
  s = Studend("지민")
  s.add_score(90)
  s.add_score(80)
  print(f"{s.name}의 평균점수는 {s.average()}점 입니다.")
  ```

- 클래스 변수와 인스턴스 변수

  ```python
  class Student:
    count = 0 # 클래스 변수(모든 인스턴스 공유)
    
    def __init__(self, name):
      self.name = name
      Student.count += 1
      
    def show(self):
      print(f"{self.name} 학생입니다.")
      
  # 테스트
  s1 = Student("철수")
  s2 = Student("영희")
  print("총 학생 수: ", Student.count)
  ```

- 상속

  ```python
  class Animal:
    def __init__(self, name):
      self.name = name
      
    def speak(self):
      print("소리를 낸다.")
      
  class Dog(Animal):
    def speak(self):
      print(f"{self.name}가 멍멍 짓는다.")
      
  class Cat(Animal):
    def speak(self):
      print(f"{self.name}가 야옹 울다.")
      
  # 사용
  d = Dog("멍멍이")
  c = Cat("야옹이")
  d.speak()
  c.speak()
  ```

- 캡슐화와 getter/setter

  ```python
  class BankAccount:
    def __init__(self, owner, balance):
      self.owner = owner
      self._balance = balance # private 변수
      
    def deposit(self, amount):
      self._balance += amount
      
    def withdraw(self, amount):
      if amount <= self._balance:
        self._balance -= amount
      else:
        print("잔액 부족")
        
    def get_balance(self):
      return self._balance
    
  # 사용
  acc = BankAccount("edo", 10000)
  acc.deposit(3000)
  print("잔액: ", acc.get_balance())
  acc.withdraw(15000)
  ```

#### 예외처리 / 라이브러리

- 예외처리

  ```python
  try:
    num = int(input("숫자를 입력하세요: "))
    result = 10 / num
  except ValueError:
    print("숫자가 아닙니다.")
  except ZeroDivisionError:
    print("0으로 나눌 수 없습니다.")
  else:
    print(f"결과: {result}")
  finally:
    print("연산이 완료되었습니다.")
  ```

#### 입력 변환 패턴

- 기본입력 :  문자열 그대로 받음

  ```python
  a = input()
  ```

- 정수입력 : 한 줄에 정수 하나 들어올 때

  ```python
  a = int(input())
  ```

- 여러 정수입력 : 공백으로 구분된 여러 정수 받을 때 

  예: 입력 1 2 3 4 :arrow_right: [1, 2, 3, 4]​

  ```python
  arr = list(map(int, input().split()))
  ```

- 여러 줄 입력 : 한 줄에 하나씩 정수 n개 받을 때

  ```python
  n = int(input())
  arr = [int(input()) for _ in range(n)]
  ```

- 문자열 리스트 : 문자열을 문자 단위 리스트로 변환

  ```python
  arr_lst = input().split()
  ```

- 2차원 배열 입력

  ```python 
  n, m = map(int, input().split())
  matrix = [list(map(int, input().split())) for _ in range(n)]
  ```

  ```
  3 3
  1 2 3
  4 5 6
  7 8 9
  ```

- 딕셔너리 입력

  ```python
  d = dict([input().split() for _ in range(3)])
  ```

- 빠른 입력 : 여러 줄 대량 입력 받을 때 속도 빠름

  ```python
  import sys
  input = sys.stdin.readline
  ```

### AI 파이썬 응용

#### 1) socket 통신

:bulb: Socket datagram 사용은 소규모 정보 통신(중요하진 않지만 대량으로 왔다갔다하는 데이터), 중요한 데이터는 모두 TCP/IP 통신임

##### 1-1) 공용서버에서만 하는 경우

- echo_server.py

  ```python
  # echo_server.py
  import socket
  
  # 서버 소켓 생성 (IPv4, TCP)
  server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM) # SOCK_STREAM : TCP방식으로 통신
  server_socket.bind(('localhost', 5001))  # 포트 5001 사용
  server_socket.listen(1)
  
  print("Echo Server Started!")
  
  # 클라이언트 접속 대기
  client_socket, addr = server_socket.accept()
  print(f"Client connected: {addr}")
  
  while True:
      data = client_socket.recv(1024)  # 데이터 수신
      if not data:
          break
      print(f"Received: {data.decode()}")
  
      # 받은 데이터를 그대로 다시 돌려줌 (Echo)
      client_socket.send(bytes("from server-> ", "utf-8") + data)
  
  # 연결 종료
  client_socket.close()
  server_socket.close()
  ```

- echo_client.py

  ```python
  # echo_client.py
  import socket
  
  # 클라이언트 소켓 생성 (IPv4, TCP)
  client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM) # SOCK_STREAM : TCP방식으로 통신
  client_socket.connect(('localhost', 5001))  # 서버와 동일한 포트 5001
  
  while True: # 항상 대기하고 있어야하는 부분때문에 while
      msg = input("Enter message: ")
      if msg == "quit":
          break
  
      # 메시지 전송
      client_socket.send(msg.encode())
  
      # 서버에서 돌려준 메시지 출력
      data = client_socket.recv(1024) # stream이 아니라 byte사용
      print(f"Echo: {data.decode()}")
  
  # 연결 종료
  client_socket.close()
  ```

- 실행

  (공용서버 터미널1에서) $ python3 echo_server.py

  (공용서버 터미널2에서) $ python3 echo_client.py

  $ netstat -nlpt | grep python

##### 1-2) 공용서버 - 개인서버 통신하는 경우

- 서버는 개인, 구동은 공용(이 경우엔 echo_server는 개인서버에서 실행, echo_client는 공용서버에서 실행) or 서버는 공용 구동은 개인으로(이 경우 echo_server는 공용서버에서 실행, echo_client는 개인서버에서 실행) 해보는 케이스

  => OCI에서 데이터 수신받고 파일로 저장하는 흐름 이해를 위함

- 먼저 scp를 사용해서 공용서버에서 만든 echo*.py 파일을 개인서버로 옮겨준다.

  case1 ) `scp user@가져올서버의IP:filepath 가져올위치`
  $ scp edu004@158.247.210.130:~/home/edu004/work/*.py . : (개인 서버에서 공유 서버에 있는 파일을 끌고 올 경우) 

  case2 ) `scp 보낼파일경로 개인서버유저@개인서버IP`
  $ scp -i ~/ssh-key-2025-09-23.key echo*.py ubuntu@130.162.146.139:~/work : (공용 서버에서 파일을 개인서버로 보낼 경우) ssh 키로 접근이 가능하기 때문에 ssh 키 경로 작성해주고 넣어준다.

- :star: 방화벽은 공용서버뿐만이 아니라 개인서버에도 존재한다. 따라서 개인서버에서 실행할 경우 방화벽 열어줘야함
  $ sudo iptables -I INPUT 1 -p tcp --dport 5000:9000 -j ACCEPT
  공유서버는(강사님이 열어주심) OCI라서
  $ ufw allow 5000:9000/tcp

- echo_server.py, echo_client.py에 설정한 IP주소를 변경해주어야 한다.

  Case1) 공유서버에서 서버를 실행할 경우 공유서버의 echo_server는 0.0.0.0으로, 해당 서버에 접근하려는 개인서버의 echo_client는 공유서버의 IP(158.247.210.130)로 변경

  Case2 ) 개인서버에서 실행하는 echo_server는 0.0.0.0 으로, 공유서버에서 실행하는 echo_client는 개인서버 IP(130.162.146.139)로 변경

  ```python
  # echo_server.py
  import socket
  
  server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  server_socket.bind(('0.0.0.0', 5001))  # 모든 접속을 허용한다는 의미
  server_socket.listen(1)
  
  print("Echo Server Started!")
  
  # 클라이언트 접속 대기
  client_socket, addr = server_socket.accept()
  print(f"Client connected: {addr}")
  
  while True:
      data = client_socket.recv(1024)  # 데이터 수신
      if not data:
          break
      print(f"Received: {data.decode()}")
  
      # 받은 데이터를 그대로 다시 돌려줌 (Echo)
      client_socket.send(bytes("from server-> ", "utf-8") + data)
  
  # 연결 종료
  client_socket.close()
  server_socket.close()
  ```

  ```python
  # echo_client.py
  import socket
  
  client_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  client_socket.connect(('158.247.210.130', 5001))  # 공유 서버에 접속해야하니 공유 서버 IP를 작성해준다.
  
  while True:
      msg = input("Enter message: ")
      if msg == "quit":
          break
  
      # 메시지 전송
      client_socket.send(msg.encode())
  
      # 서버에서 돌려준 메시지 출력
      data = client_socket.recv(1024) # stream이 아니라 byte사용
      print(f"Echo: {data.decode()}")
  
  # 연결 종료
  client_socket.close()
  ```

- (개인서버에서) $ python3 echo_server.py

  (공용서버에서) $ python3 echo_client.py

  $ netstat -nlpt  | grep python | wc -l
  여기서 Local Address가 127:0:0:1 인 경우는, localhost라서 해당 IP에서만 접근이 가능. 0:0:0:0 인 경우는 다른 모든곳에서의 접근을 허용해준 것

- 누가 내 ID쓰는지 확인방법
  netstat -nlpt | grep python에서 PID확인
  ps -ef | grep PID

  ##### 1-3) 공용 - 개인 기능추가하기(w기능)

  ```python
  import socket
  server_socket = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  server_socket.bind(('0.0.0.0', 5004))
  server_socket.listen(1)
  print("Echo Server Started!")
  client_socket, addr = server_socket.accept()
  print(f"Client connected: {addr}")
  
  with open("echo_recv.txt", "w", encoding="utf-8") as f:
      while True:
          data = client_socket.recv(1024)
          if not data:
              break
          print(f"Received: {data.decode()}") # 여기서 data는 바이트
          f.write(data.decode()) 
      # with open("result.txt", "w", encoding="utf-8") as f:
          # f.write(data.decode())
      client_socket.send(bytes("from server->", "utf-8")+data)
  client_socket.close()
  server_socket.close()
  ```

  #### 2) JSON

  - JSON 예제

    ```python
    import json
    
    data = {"name": "Alice", "age": 25}
    
    # 쓰기
    with open("data.json", "w") as f:
      json.dump(data, f)
    
    import json
    # 읽기
    with open("data.json", "r") as f:
      loaded = json.load(f)
      print(loaded)
    ```

    