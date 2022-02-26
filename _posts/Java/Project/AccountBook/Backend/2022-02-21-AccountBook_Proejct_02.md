---
title: "Spring Boot 시작-생성"
categories:
  - Project
tags:
  - ToyProject
  - AccountBook
  - Project
sidebar:
  nav: "accountBookProject"
---
저번 포스팅에 이어서 이제 Spring Boot 프로젝트를 설치하려 한다.  
일단, 이클립스를 키고 Workspace를 잡는다. 보통은 이클립스 설치 후에 Workspace를 처음 지정후  
다시 물어보지 않음을 클릭하여 다시 물어보지 않도록 할 것이다.  
하지만, 나는 git 연결용 Project를 따로 관리하기 위해서 별도 경로를 사용할 것이다.  
그래서, Workspace를 지정하도록 풀었다.  
  
***
> ### 프로젝트 생성  
  
프로젝트 생성을 해보자. [File]->[New]->[Spring Starter Project]를 선택한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_001.png" alt="01">
<br>
</div>
<br>
프로젝트의 이름, 타입, 자바 버전등등을 설정 후 Next를 누른다.  
나는 Gradle, Java 11을 선택 후 나머지 이름은 적당히 지었다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_002.png" alt="02">
<br>
</div>
  
<br>
그 다음, 스프링부트의 버전과 필요한 Dependencies들을 선택한다.  
Lombok, PostgreSQL Driver, JPA, Spring Web, Spring HATEOAS 를 선택했다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_003.png" alt="03">
<br>
</div>
  
Finish를 하자.  
잠깐만 기다리면 프로젝트 Explorer에 내가 지정한 프로젝트가 나타난다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_004.png" alt="04">
<br>
</div>
  
***
> ### 프로젝트 실행  
  
이제 프로젝트로 만들었으니 실행을 해보자.  
Spring Boot의 구동시작점은 @SpringBootApplication 어노테이션이 있는 Application.java가 진입점이다. 해당 파일위에서 우클릭 후 [Run As]->[Spring Boot App]으로 실행을 해보자.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_005.png" alt="05">
<br>
</div>
<br>
아마 당연하게도 실행이 안될것이다. 이유는 PostgreSQL Driver를 설치했지만, 데이터베이스 접속에 대한 정보가 없어 데이터베이스를 접속하지 못했기 때문이다.  

```
***************************
APPLICATION FAILED TO START
***************************

Description:

Failed to configure a DataSource: 'url' attribute is not specified and no embedded datasource could be configured.

Reason: Failed to determine a suitable driver class


Action:

Consider the following:
	If you want an embedded database (H2, HSQL or Derby), please put it on the classpath.
	If you have database settings to be loaded from a particular profile you may need to activate it (no profiles are currently active).


```  
(초기에 로딩시키지 않고 나중에 사용하는 방법이 있는듯 하다. 좀 더 찾아봐야할듯...)
데이터베이스를 연결할 정보가 없기 때문이다.  application.properties를 수정하자. 근데 나는 yaml 형태로된 정보를 읽는게 편하기 때문에 application.yml로 확장자를 변경한다.  
  
resources폴더안에 있는 application.properties를 application.yml로 변경한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_006.png" alt="06">
<br>
</div>
<br>
  
파일을 열면 아무 내용도 없는 빈 파일이다. 여기에 내용을 적어주자.

``` yaml
spring:
  datasource.username: 계정
  datasource.password: 암호
  datasource:
    url: jdbc:postgresql://서버주소:포트/데이터베이스명
  sql:
    init:
      encoding: utf-8
  
```  
위의 정보대로 입력후  다시 실행시키면 정상적으로 동작할 것이다. 만약, 이렇게 했는데도 제대로 동작하지 않는다면, PC에 설치한 PostgreSQL이 정상적으로 동작하고 있는지 확인한다.  
  
나는 아래와 같은 설정 정보를 사용한다.

``` yaml
spring:
  jpa:
    show-sql: true
    hibernate:
      ddl-auto: update
    generate-ddl: true
  datasource.username: 계정
  datasource.password: 암호
  datasource:
    url: jdbc:postgresql://서버주소:포트/데이터베이스명
  sql:
    init:
      encoding: utf-8
  main:
    allow-bean-definition-overriding: true
server:
  servlet:
    context-path: /api
```  

***
> ### Github 등록  
  
프로젝트도 정상적으로 동작하는 것을 확인했으니, 이제 Github에 이 프로젝트를 등록해보도록 하자.  
  
먼저, GitHub Repository에 내가 등록할 Repository 이름을 설정한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_007.png" alt="07">
<br>
</div>
<br>
  
생성이 완료되었으면 이클립스로 돌아가서, GitRepository Explorer 화면을 켜야한다. 
[Window]->[Show View]->[Other]를 클릭한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_008.png" alt="08">
<br>
</div>
<br>
  
Git Repositories를 선택후 Open을 클릭한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_009.png" alt="09">
<br>
</div>
<br>
  
이 글을 작성하면서 Github 로그인을 하려고 했는데, 로그인이 안되었다. 이유는 기존 ID/PWD 방식을 통한 로그인은 더 이상 지원하지 않는다고 한다.(2021.08.13)  
GitHub에서 토큰을 생성하여 이용해야한다.  
  
1. GitHub 홈페이지 접속 -> 오른쪽 상단 프로필 -> Settings 선택  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_010.png" alt="10">
<br>
</div>
<br>
  
2. Developer Settings 메뉴 선택  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_011.png" alt="11">
<br>
</div>
<br>
  
3. Personal access tokens -> Generate new token 선택  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_012.png" alt="12">
<br>
</div>
<br>
  
4. 토큰 명 작성 -> 토큰 유효기간 선택 -> 토큰 허용 범위 선택  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_013.png" alt="13">
<br>
**주의할 점은 토큰 생성하고나서는 해당 값을 다시 볼 수 없기 때문에 생성된 토큰값을 잘 보관해야한다.**  
</div>
<br>
  
얻은 암호값으로 로그인을 하면 Git에 정상적으로 로그인 할 수 있다.  

[Clone Git Repository]를 클릭한다. 이미 GitHub에 Repository를 만들어 놨기 때문에, Create가 아닌 Clone을 이용하는 것이다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_014.png" alt="14">
<br>
</div>
<br>
  
GitHub Repository 주소를 입력하고, ID와 아까 토큰 정보를 입력한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_015.png" alt="15">
<br>
</div>
<br>
  
Branch 종류를 선택하고  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_016.png" alt="16">
<br>
</div>
<br>
  
로컬에 저장할 위치를 지정후 저장한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_017.png" alt="17">
<br>
</div>
<br>
  
이클립스 프로젝트의 우클릭 후 [Team]->[Share Project]를 선택하면, 어떤 종류의 로컬 Git을 연동할 것인지 선택한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_018.png" alt="18">
<br>
<br>
<img src="/assets/images/posts/Project/AccountBook/post_02_019.png" alt="19">
<br>
<br>
</div>
<br>
  
정상적으로 Git 관리가 되었다면 프로젝트 구성 파일의 색상이 변경되었을 것이다.  (Git에 아직 Push하지 않은 데이터는 이렇게 표시된다.)  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_020.png" alt="20">
<br>
</div>
<br>
  
[Team]->[Push to origin]을 클릭하면  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_021.png" alt="21">
<br>
</div>
<br>
  
변경이된 파일에 대한 리스트가 표시되며, ++ 버튼을 클릭하여 스테이지 쪽으로 추가한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_02_022.png" alt="22">
<br>
</div>
<br>
  
그 후 Commit and Push 버튼을 클릭하여 커밋과 푸쉬를 같이 진행하게되면 Git 연동은 완료된다.  
  
이제 프로젝트 진행을 위한 가장 기초적인 작업은 완료되었다.  
  
다음 포스팅에서는 JPA의 개념과 이를 이용하는 방법에 대해 포스팅하겠다.