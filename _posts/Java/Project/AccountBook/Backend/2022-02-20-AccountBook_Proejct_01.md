---
title: "Spring Boot 시작-설치"
categories:
  - Project
tags:
  - ToyProject
  - AccountBook
  - Project
sidebar:
  nav: "accountBookProject"
---
SpringBoot의 시작부터 진행하려고 한다. SpringBoot를 아래와 같이 구성품으로 구성하려고한다.  
- SpringBoot  
- PostgreSQL  
- Gradle
- Spring HETEOS
- Spring Security -> 나중에 구성
- JPA
- Spring OAuth2.0 -> 나중에 구성


***
> #### 1. 이클립스에 STS 4.x 설치  
  
Eclipse에 STS 4.x를 설치해야 SpringBoot 이용이 가능하다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_01_001.png" alt="01">
<br>
[Help->Eclipse Marketplace]  
</div>
  
Find에 "Spring"을 입력 후 엔터를 입력한다. 그후에 Install버튼을 눌려 설치를 진행한다.  
<div align="center">
<img src="/assets/images/posts/Project/AccountBook/post_01_002.png" alt="02">
<br>
<br>
<img src="/assets/images/posts/Project/AccountBook/post_01_003.png" alt="03">
<br>
<br>
<img src="/assets/images/posts/Project/AccountBook/post_01_004.png" alt="04">
<br>
<br>
<img src="/assets/images/posts/Project/AccountBook/post_01_005.png" alt="05">
<br>
[설치완료 후 재실행]
</div>

***
> #### 2. Oracle JDK 11버전 설치  
  
회사에서는 OpenJDK를 사용하지만, 어차피 이건 일반 컴퓨팅용이기도 하고, 내가 돈주고 팔것도 아니기 때문에, 그냥 익숙한 Oracle JDK를 설치하기로 한다.  
  
[오라클 자바 11 SE 다운로드](https://www.oracle.com/kr/java/technologies/javase/jdk11-archive-downloads.html)  
  
여기에서 윈도우용 버전을 다운로드 받는다. 설치 괒어은 별것없기에, 스크린샷은 제외한다.  
다만, 오라클은 로그인을 해야한다. (까먹으면 귀찮다.)  
OpenJDK를 사용해도 된다.  
  
***
> #### 3. PostgreSQL 설치  
  
나중에 이 프로젝트르를 AWS에 올려볼 생각이다. 그래서 처음부터 MSSQL Express가 아닌, PostgreSQL을 이용하는 것으로 잡았다.  
  
[PostgreSQL 다운로드](https://www.enterprisedb.com/downloads/postgres-postgresql-downloads)  

그냥 최신버전 받았다. 크게 상관은 없다.  
  

***
> #### 4. Git 설치  
  
이클립스에서는 기본으로 Git관련 도구를 지원한다. 다만, 윈도우에도 Git을 사용하기 위해서 프로그램을 설치한다.  
  
[Git 다운로드](https://git-scm.com/downloads)  

윈도우용 다운로드 받아서 설치하면 완료.
  
  
다음 포스팅은 Spring Boot 프로젝트를 생성하고 Git에 연결하는 과정을 설명하겠다.