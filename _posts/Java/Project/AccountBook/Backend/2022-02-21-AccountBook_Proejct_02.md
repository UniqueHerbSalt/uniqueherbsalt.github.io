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
  
### 프로젝트 생성  
프로젝트 생성을 해보자. [File]->[New]->[Spring Starter Project]를 선택한다.  
![01]({{'/assets/images/posts/Project/AccountBook/post_02_001.png'| relative_url }})  
  
프로젝트의 이름, 타입, 자바 버전등등을 설정 후 Next를 누른다.  
나는 Gradle, Java 11을 선택 후 나머지 이름은 적당히 지었다.  
![02]({{'/assets/images/posts/Project/AccountBook/post_02_002.png'| relative_url }  
  
그 다음, 스프링부트의 버전과 필요한 Dependencies들을 선택한다.  
Lombok, PostgreSQL Driver, JPA, Spring Web, Spring HATEOAS 를 선택했다.  
![03]({{'/assets/images/posts/Project/AccountBook/post_02_003.png'| relative_url }  
  
Finish를 하자.  
잠깐만 기다리면 프로젝트 Explorer에 내가 지정한 프로젝트가 나타난다.  
![04]({{'/assets/images/posts/Project/AccountBook/post_02_004.png'| relative_url }  
  
### 프로젝트 실행  
이제 프로젝트로 만들었으니 실행을 해보자.  
Spring Boot의 구동시작점은 @SpringBootApplication 어노테이션이 있는 Application.java가 진입점이다. 해당 파일위에서 우클릭 후 [Run As]->[Spring Boot App]으로 실행을 해보자.  
![05]({{'/assets/images/posts/Project/AccountBook/post_02_005.png'| relative_url }  
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
![06]({{'/assets/images/posts/Project/AccountBook/post_02_006.png'| relative_url }  
  
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

### Github 등록  
프로젝트도 정상적으로 동작하는 것을 확인했으니, 이제 Github에 이 프로젝트를 등록해보도록 하자.  
  
먼저, GitHub Repository에 내가 등록할 Repository 이름을 설정한다.  
![07]({{'/assets/images/posts/Project/AccountBook/post_02_007.png'| relative_url }  
  
생성이 완료되었으면 이클립스로 돌아가서, GitRepository Explorer 화면을 켜야한다. 
[Window]->[Show View]->[Other]를 클릭한다.  
![08]({{'/assets/images/posts/Project/AccountBook/post_02_008.png'| relative_url }  
  
Git Repositories를 선택후 Open을 클릭한다.  
![09]({{'/assets/images/posts/Project/AccountBook/post_02_009.png'| relative_url }  
    
이 글을 작성하면서 Github 로그인을 하려고 했는데, 로그인이 안되었다. 이유는 기존 ID/PWD 방식을 통한 로그인은 더 이상 지원하지 않는다고 한다.(2021.08.13)  
GitHub에서 토큰을 생성하여 이용해야한다.  
  
1. GitHub 홈페이지 접속 -> 오른쪽 상단 프로필 -> Settings 선택  
![10]({{'/assets/images/posts/Project/AccountBook/post_02_010.png'| relative_url }  
  
2. Developer Settings 메뉴 선택  
![11]({{'/assets/images/posts/Project/AccountBook/post_02_011.png'| relative_url }  
  
3. Personal access tokens -> Generate new token 선택  
![12]({{'/assets/images/posts/Project/AccountBook/post_02_012.png'| relative_url }  
  
4. 토큰 명 작성 -> 토큰 유효기간 선택 -> 토큰 허용 범위 선택  
![13]({{'/assets/images/posts/Project/AccountBook/post_02_013.png'| relative_url }  
**주의할 점은 토큰 생성하고나서는 해당 값을 다시 볼 수 없기 때문에 생성된 토큰값을 잘 보관해야한다.**  
  
얻은 암호값으로 로그인을 하면 Git에 정상적으로 로그인 할 수 있다.  

[Clone Git Repository]를 클릭한다. 이미 GitHub에 Repository를 만들어 놨기 때문에, Create가 아닌 Clone을 이용하는 것이다.  
![14]({{'/assets/images/posts/Project/AccountBook/post_02_014.png'| relative_url }  
  
GitHub Repository 주소를 입력하고, ID와 아까 토큰 정보를 입력한다.  
![15]({{'/assets/images/posts/Project/AccountBook/post_02_015.png'| relative_url }  
  
Branch 종류를 선택하고  
![16]({{'/assets/images/posts/Project/AccountBook/post_02_016.png'| relative_url }  
  
로컬에 저장할 위치를 지정후 저장한다.  
![17]({{'/assets/images/posts/Project/AccountBook/post_02_017.png'| relative_url }  
  
이클립스 프로젝트의 우클릭 후 [Team]->[Share Project]를 선택하면, 어떤 종류의 로컬 Git을 연동할 것인지 선택한다.  
![18]({{'/assets/images/posts/Project/AccountBook/post_02_018.png'| relative_url }  
![19]({{'/assets/images/posts/Project/AccountBook/post_02_019.png'| relative_url }  
  
정상적으로 Git 관리가 되었다면 프로젝트 구성 파일의 색상이 변경되었을 것이다.  (Git에 아직 Push하지 않은 데이터는 이렇게 표시된다.)  
![20]({{'/assets/images/posts/Project/AccountBook/post_02_020.png'| relative_url }  
  
[Team]->[Push to origin]을 클릭하면  
![21]({{'/assets/images/posts/Project/AccountBook/post_02_021.png'| relative_url }  
  
변경이된 파일에 대한 리스트가 표시되며, ++ 버튼을 클릭하여 스테이지 쪽으로 추가한다.  
![22]({{'/assets/images/posts/Project/AccountBook/post_02_022.png'| relative_url }  
  
그 후 Commit and Push 버튼을 클릭하여 커밋과 푸쉬를 같이 진행하게되면 Git 연동은 완료된다.  
  
이제 프로젝트 진행을 위한 가장 기초적인 작업은 완료되었다.  
  
다음 포스팅에서는 JPA의 개념과 이를 이용하는 방법에 대해 포스팅하겠다.