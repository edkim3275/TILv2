# PM2

## PM2?

> "Advanced, production process manger for node.js"
>
> PM2 is a daemon process manager that will help you manage and keep your application online 24/7

PM2는 Node.js 애플리케이션을 실행하고 관리하는 프로세스 매니저(PM, Process Manager)

프로세스는 컴퓨터에 저장되어있는 프로그램이 실행이 돼서 메모리에 올라가있는 것. 즉, 동작중인 프로그램을 프로세스라고 한다. 이를 관리해주는 도구가 PM2

## PM2 주요기능

1. 애플리케이션 관리

   애플리케이션을 시작하고, 중지하고, 재시작할 수 있다. 또한 여러 애플리케이션을 실행할 수 있고, 애플리케이션 간의 종속성을 관리할 수 있다.

2. 로그 관리

   애플리케이션의 로그 관리. 애플리케이션에서 발생하는 이벤트 추적 및 로그 파일 검색

3. 프로세스 클러스터링

   Node.js 애플리케이션을 클러스터링하여 다수의 코어를 사용한다. 이를 통해 Node.js 애플리케이션은 멀티 프로세싱을 지원하며 성능을 향상시킨다.

4. 스케일링

   애플리케이션 인스턴스를 자동으로 확장하거나 축소할 수 있다.