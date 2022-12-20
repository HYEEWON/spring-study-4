# Web Application

<br>

## 목차
* [웹 서버 (Web Server)](#웹-서버-web-server)
* [WAS (Web Application Server)](#was-web-application-server)
* [웹 서버와 WAS의 차이](#------was----)
* [웹 시스템 구성](#웹-시스템-구성)
* [WAS의 기능](#was----)
* [Servlet](#servlet)
* [Servlet Container](#servlet-container)
* [Thread](#thread)
* [요청마다 Thread를 생성하는 경우](#-----thread---------)
* [Thread Pool을 사용하는 경우](#thread-pool을-사용하는-경우)
* [WAS의 Multi Thread](#was--multi-thread)

<br>

## 웹 서버 (Web Server)
* HTTP를 기반으로 동작
* 정적 리소스 (파일) 제공
* 정적 리소스: 특정 폴더에 HTML, CSS, JS, 이미지, 영상 등의 파일을 두면 서버가 전달함
* ex) Nginx, Apache

<br>

## WAS (Web Application Server)
* 웹 서버의 기능을 대부분 포함 (정적 리소스 제공 가능)
* 프로그램 코드를 실행해 애플리케이션 로직을 수행
  * 사용자마다 다른 응답을 할 수 있음
  * 동적 HTML, REST API
  * Servlet, JSP, 스프링 MVC
* ex) Tomcat

<br>

## 웹 서버와 WAS의 차이
* 두 개념의 경계는 모호함
  * WAS가 웹 서버의 기능을 제공하기도 함
  * 웹 서버도 프로그램을 실행하는 기능을 제공하기도 함
> 웹 서버는 정적 리소스 처리, WAS는 애플리케이션 로직 처리
> WAS는 애플리케이션 코드를 실행하는데 더 특화되어 있음

<br>

## 웹 시스템 구성
### 👉 WAS + DB
```
Client -> WAS -> DB
```
* WAS가 많은 역할을 담당해 서버 과부하 우려가 있음
* 비싼 애플리케이션 로직이 정적 리소스 때문에 수행이 어려울 수 있음
* WAS 장애 시에 오류 화면 제공 불가능

### 👉 Web Server + WAS + DB
```
Client -> Web Server -> WAS -> DB
```
* 정적 리소스는 웹 서버가 처리 + 애플리케이션 로직은 WAS가 처리
  * 애플리케이션 로직과 같은 동적인 처리가 필요하면 웹 서버가 WAS에 요청 위임
* 효율적인 리소스 관리 가능
  * 정적 리소스가 많이 사용되면 웹 서버 증설
  * 애플리케이션 리소스가 많이 사용되면 WAS 증설
* 웹 서버는 잘 죽지 않지만, WAS는 잘 죽음
* 웹 서버는 WAS, DB 장애 시에 사용자에게 오류 화면을 제공할 수 있음 (오류화면 HTML)

<br>

## WAS의 기능
> Servlet 지원
* 비지니스 로직을 제외한 모든 것을 자동화
  * TCP/IP 대기, 소켓 연결 ~ HTTP 요청 메시지 파싱
  * HTTP 응답 메시지 생성 ~ TCP/IP 응답 전달, 소켓 종료

> Multi Thread 지원
* 개발자가 Multi Thread 관련 코드를 신경쓰지 않아도 됨
* 개발자는 Single Thread인 것처럼 소스 코드 개발
* Multi Thread 환경에서는 싱글톤 객체(Servlet, Spring Bean)는 주의해서 사용

<br>

## Servlet
> 개발자는 HTTP 스펙을 편리하게 사용할 수 있음
* HttpServletRequest: HTTP 요청 정보를 편리하게 사용
* HttpServletResponse: HTTP 응답 정보를 편리하게 사용

### 👉 HTTP 요청/응답 흐름
* HTTP 요청 발생
* WAS는 Request, Response 객체를 생성, 이것을 파라미터로 Servlet 객체 호출/실행
* 개발자는 Request, Response 객체에서 HTTP 요청/응답 정보를 편리하게 사용
* WAS는 Response 객체에 담겨있는 내용으로 HTTP 응답 정보 생성
* HTTP 응답 전달

<br>

## Servlet Container
* Servlet을 지원하는 WAS
* Servlet 객체 생성/호출/종료 등의 생명주기 관리
* Servlet 객체는 Singleton으로 관리
* 사용자의 요청이 올 때마다 Servlet 객체를 생성하는 것은 비효율적 -> 최초 로딩 시점에 만들어 재활용
* 모든 사용자의 요청은 동일한 Servlet 객체 인스턴스에 접근
  * 공유 변수 사용을 주의해야 함
* Servlet Container 종료 시 종료됨
* 동시 요청 처리를 위한 Multi-Thread 지원

<br>

## Thread
* Thread가 Servlet을 호출함
• 애플리케이션 코드를 순차적으로 실행하는 역할
• 자바 메인 메서드를 처음 실행하면 main이라는 이름의 Thread가 실행
• Thread가 없다면 자바 애플리케이션 실행 불가능
• Thread는 한번에 하나의 코드 라인만 수행
• 동시 처리가 필요하면 쓰레드를 추가로 생성

> 요청이 오면 쓰레드 할당, 서블릿 호출

<br>

## 요청마다 Thread를 생성하는 경우
### 장점
* 동시 요청 처리 가능
* 리소스가 허용할 때까지 처리 가능
* 하나의 Thread가 지연되어도 나머지는 정상 동작함

### 단점
* Thread 생성비용이 큼
  * 요청마다 Thread를 생성하면 응답 속도가 느려짐
* Thread는 Context Switch 비용 발생
* Thread 생성에 제한이 없음
  * 사용자의 요청이 너무 많을 경우, CPU/메모리 한계를 넘어 서버가 죽을 수 있음

> -> WAS는 Thread Pool을 사용함

<br>

## Thread Pool을 사용하는 경우
* 필요한 Thread를 Thread Pool에 보관/관리
* Thread가 필요하면, 이미 생성되어 있는 Thread를 Thread Pool에서 꺼내서 사용하고 반납
* 남은 Thread가 없다면 이후의 요청은 거절하거나 특정 수의 요청은 대기 가능
* 장점
  * Thread가 미리 생성되어 있어 Thread의 생성/종료 비용 절약, 응답 시간이 빨라짐
  * 생성 가능한 Thread의 수가 제한되어 있어 많은 요청이 들어와도 안전하게 처리 가능
* 최대 Thread(Max Thread) 튜닝
  * 너무 낮게 설정 -> 동시 요청이 많을 때, 서버 리소스는 여유롭지만 클라이언트는 응답 지연
  * 너무 높게 설정 -> 동시 요청이 많을 때, CPU/메모리 리소스 임계점 초과로 서버 다운
  * 장애 발생 시 -> 클라우드면 서버 증설 + 튜닝, 클라우드가 아니면 튜닝
  * 성능 테스트를 통해 적절한 수를 찾아야 함

<br>

## WAS의 Multi Thread
* Multi Thread는 WAS가 처리
* 개발자가 Multi Thread 관련 코드를 신경쓰지 않아도 됨
* Singleton 객체는 주의해서 사용해야 함
