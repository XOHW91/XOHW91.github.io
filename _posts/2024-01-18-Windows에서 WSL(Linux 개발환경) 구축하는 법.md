---
layout: single
title:  "[Server] Windows에서 WSL(Linux 개발환경) 구축하는 법"
typora-root-url: ../
---





# WSL(Windows Subsystem for Linux)이란?

WSL란 Windows 운영 체제에서 Linux 환경을 실행할 수 있도록 하는 기술로, Windows10부터 도입됐으며 사용자들이 Windows에서 Linux 명령어 및 유틸리티를 실행하고 Linux 배포판을 설치할 수 있도록 한다. 
Virtual Box같은 가상머신을 사용하지 않고도 개발자들이 Windows에서 Linux 기반의 개발 작업이 가능하도록 하는데 목적이 있다.

나같은 경우도 회사 컴퓨터 OS가 Windows인데 Linux 환경에 대한 공부&실습을 하기 위해 설치하게 됐다. (사수의 권유로!)



#### **설치방법**

[]: https://learn.microsoft.com/en-us/windows/wsl/install

설치 자체는 어렵지 않아서 MS 공식 문서를 참고하면 금방 설치가 가능하지만, 아래에서 꼼꼼하게 기록해보려고 한다. 
Ubuntu 설치 후 mariadb, apache2, tomcat9 설치까지의 내용을 담았다.



`STEP 1`  

Windows 키 + Pause 를 눌러서 시스템 창을 키고, 시스템 > 선택적 기능 > 기타 Windows 기능 클릭  

![image-20240118091609865](/images/2024-01-18-HTTP-API-설계예시/image-20240118091609865.png)



`STEP 2 ` 선택적 기능 창에서 아래 기능을 체크하고 확인 > 컴퓨터 재시작 

- Linux용 Windows 하위 시스템 
- Windows 하이퍼바이저 플랫폼
- 가상 머신 플랫폼

![image-20240118091402326](/images/2024-01-18-HTTP-API-설계예시/image-20240118091402326.png)



`STEP 3 ` 명령 프롬프트 실행 

![image-20240118092219787](/images/2024-01-18-HTTP-API-설계예시/image-20240118092219787.png)



`STEP 4` 

**wsl --list --online** 명령어를 입력하면 설치할 수 있는 배포판 목록이 나온다.
여기서 나는 맨 위의 **Ubuntu** 로 설치 진행했다.

![image-20240118092526223](/images/2024-01-18-HTTP-API-설계예시/image-20240118092526223.png)




`STEP 5 `

- **wsl --install -d Ubuntu** 로 Ubuntu 설치

![image-20240118092927952](/images/2024-01-18-HTTP-API-설계예시/image-20240118092927952.png)



- 나같은 경우에는 설치완료하고나니 Error 떴다. ==> WslRegisterDistribution failed with error: 0x800701bc
  찾아보니 Linux 커널 업데이트 패키지를 다운로드 하지 않아서 발생하는 오류

  **wsl --update** 명령어 실행 > 설치완료하니 에러는 사라짐.

![image-20240118093002277](/images/2024-01-18-HTTP-API-설계예시/image-20240118093002277.png)




`STEP 6 `

- 명령프롬프트 창에서 아래같이 화살표 눌러서 Ubuntu창을 띄움.

![image-20240118093434118](/images/2024-01-18-HTTP-API-설계예시/image-20240118093434118.png)



- 접속할 username과 password를 입력해준다.
  ![image-20240118093732794](/images/2024-01-18-HTTP-API-설계예시/image-20240118093732794.png)



`STEP 7 ` 설치 명령 순서 (순서대로 진행하면 됨)

- **sudo :** 사용자가 superuser, root 권한으로 명령을 실행할 수 있도록 하는 명령어다. sudo 명령어를 사용하면 일반 사용자도 일시적으로 슈퍼 유저의 권한을 얻을 수 있음. 주로 시스템 관리, 패키지 설치, 설정 변경 등의 작업에서 필요하다. 

  ⚠️ **su 명령어와의 차이점?**  sudo와는 다르게 현재 사용자 계정에서 다른 사용자로 전환하는 데 사용됨. 기본적으로 `root`계정으로 전환되는 데 사용됨. (root 비번 필요) 
  
- **sudo apt update :** APT (Advanced Package Tool) 패키지 관리시스템을 업데이트하는 명령어. 이 명령어를 실행하면 시스템의 패키지 목록이 최신 상태로 업데이트 됨. 이 과정을 통해 사용자는 최신 소프트웨어 및 보안 업데이트를 적용할 수 있음. 
  ![image-20240118094255817](/images/2024-01-18-HTTP-API-설계예시/image-20240118094255817.png)

  

- **sudo apt install aptitude :**  APT 패키지 관리 시스템을 통해 `aptitude`라는 패키지를 설치하는 명령어. 
  aptitude는 패키지 관리 도구 중 하나로, `apt`와 마찬가지로 패키지의 설치, 제거, 업그레이드 등을 수행하는 데 사용되며,  패키지 간의 의존성을 더욱 효과적으로 처리하는 특징이 있다. 즉, 이 명령어를 실행하면 시스템에 `aptitude` 패키지가 설치되며, 이를 이용하여 패키지 관리 및 시스템 유지보수 작업을 수행할 수 있다.

  ![image-20240118094123942](/images/2024-01-18-HTTP-API-설계예시/image-20240118094123942.png)

  

- **sudo aptitude update : **APT 패키지 관리 시스템을 통해 시스템의 패키지 목록을 최신 상태로 업데이트하는 명령어이다. 
  ![image-20240118094355783](/images/2024-01-18-HTTP-API-설계예시/image-20240118094355783.png)

  

- **aptitude search mariadb :** APT 패키지 관리 시스템을 통해 시스템에 설치 가능한 MariaDB 패키지를 검색하는 명령어
  ![image-20240118094042851](/images/2024-01-18-HTTP-API-설계예시/image-20240118094042851.png)

  

- **sudo aptitude install mariadb-server :**  APT 패키지 관리 시스템을 통해 MariaDB 서버를 설치하는 명령어

  

- **sudo mariadb-secure-installation :** MariaDB를 더 안전하게 구성하기 위한 초기 설정을 수행하는 명령어이다. 이 명령어를 실행하면 다양한 보안 관련 옵션을 설정하고 비밀번호를 설정하는 등의 단계를 따를 수 있음.

  - 루트 비밀번호 설정 : MariaDB의 루트 게정에 대한 비밀번호를 설정한다.
  - 익명 사용자 제거 : 익명으로 접속할 수 있는 사용자를 제거한다.
  - 원격 접속 금지 : 원격에서의 접속을 차단한다.
  - 테스트 데이터베이스 제거 : 테스트용으로 생성된 데이터베이스를 제거한다.
  - 권한 새로고침 : 변경된 권한을 적용하기 위해 MariaDb를 재시작한다.

  ![image-20240118094521437](/images/2024-01-18-HTTP-API-설계예시/image-20240118094521437.png)





`STEP 8 `

- mariadb 설치 후 **sudo mariadb**

![image-20240118094745201](/images/2024-01-18-HTTP-API-설계예시/image-20240118094745201.png)

- **create user test@'localhost' identified by 'test';** : `test`라는 사용자 계정을 만듬, 맨 마지막의 'test'는 비밀번호임.
  **create database testdb;** : `testdb`라는 databases 생성 
- **grant all privileges on textdb.* to test@'localhost'** : test 계정에 testdb에 대한 모든 권한 부여



`STEP 9 ` apache2, tomcat9 설치 

- **sudo aptitude install apache2 :**  APT 패키지 관리 시스템을 통해 Apache 웹 서버를 설치하는 명령어
  ![image-20240118095338952](/images/2024-01-18-HTTP-API-설계예시/image-20240118095338952.png)



- **sudo aptitude install tomcat9 :** APT 패키지 관리 시스템을 통해 Apache Tomcat 9를 설치하는 명령어
  ![image-20240118095453499](/images/2024-01-18-HTTP-API-설계예시/image-20240118095453499.png)

  



#### 이 과정을 모두 완료했다면 ✅ 설치 완료




⚠️**참고** wsl --help를 치면 여러 옵션을 볼 수 있음. 

![image-20240118092359459](/images/2024-01-18-HTTP-API-설계예시/image-20240118092359459.png)