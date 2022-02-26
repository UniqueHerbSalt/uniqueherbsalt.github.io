---
title: "Spring Boot 시작-JPA"
categories:
  - Project
tags:
  - ToyProject
  - AccountBook
  - Project
sidebar:
  nav: "accountBookProject"
---
저번 포스팅에 이어서 이제 JPA에 대해서 알아보려고한다.  
지금까지 MyBatis나 C#에서 OleDB Connection을 이용해서 날데이터를 이용하는게 익숙했었다.  
근데 먼가 JPA는 C#에서 NHibernate와 같이 객체형태로 주고 받을 수 있도록 이용하는듯하다.  
프로젝트를 JPA로 구성하기로 하였으니 이참에 JPA에 대해서 학습하기로 했다.
  
***
> ### JPA?  
JPA, Java Persistence API의 준말이다. 영어 단에서 느껴지듯이 영속성에 관련된 API인거 같다.  
JPA는 자바에서 ORM, Obejct-Relational Mapping 이라는 기술 표준으로 사용되는 인터페이스의 모음이다.  
실제로 구현된것이 아니라 구현된 클래스와 매핑을 해주기 위해서 사용되는 프레임워크 이다.  
대표적인 오픈소스로 Hibernate가 있다.  
<div align="center">
![01]({{'/assets/images/posts/Project/AccountBook/post_03_001.jpg'| relative_url }})  
</div>
  
위에서 서술한 ORM은 무엇인가?  
  
> ### ORM?  
우리가 일반적으로 알고 있는 애플리케이션 Class와 RDB(Relational DataBase)의 테이블을 매핑(연결)한다는 뜻이며, 기술적으로는 어플리케이션의 객체를 RDB 테이블에 자동으로 영속화 해주는 것이라고 보면된다.  
  
왜 사용하는 것일까?
  
* 장점
  - SQL문이 아닌 매소드를 통해서 DB를 조작할 수 있어, 개발자는 객체 모델을 이용하여 비지니스 로직을 구성하는데만, 집중할 수 있다.
  (내부적으로 쿼리가 어떻게 동작하는지 신경 쓸 필요가 없다.)
  - 쿼리와 같이 필요한 선언문, 할당 등의 부수적인 코드가 줄어들어, 각종 객체에 대한 코드를 별도로 작성하여 코드의 가독성을 높힌다.
  - 객체의 매핑정보가 결국 ERD를 그대로 옮겨놓은 것이므로, 객체를 올바르게 구성했다면, 이론상으로 ERD를 볼 필요가 없다.
  - 가장 중요한 이유중 하나로 데이터베이스의 종류가 바뀌더라도 객체를 통해서 제어하기때문에 변경할 것이 거의 없다.  

* 단점  
  - 설계를 잘못하면 난리난다.
  - 쿼리튜닝이 필요할 경우 결국 쿼리를 작성할 수 밖에 없다.
  - 러닝 커브 발생
  
<div align="center">
![02]({{'/assets/images/posts/Project/AccountBook/post_03_002.png'| relative_url }})  
Interface....  
</div>


> ### Why JPA?  
단순하게 요즘 사람들이 많이 쓰는 프레임워크라서? 물론 이것도 이유중에 하나가 될수도 있다. 사람들이 많이 쓰는 만큼 성능 검증이 확실히 되었다는 소리일꺼니까?  
  
하지만 정확히 왜 JPA를 써야하는지 확인해보자.  
JPA는 반복적인 CRUD SQL을 처리해준다. JPA는 매핑된 관계를 이용해서 SQL을 생성하고 실행하는데, 개발자는 어떤 SQL이 실행될지 생각만하면 되고, 예측도 쉽게 할 수 있다. 추가적으로 JPA는 네이티브 SQL이란 기능을 제공해주는데 관계 매핑이 어렵거나 성능에 대한 이슈가 우려되는 경우 SQL을 직접 작성하여 사용할 수 있다.  
  
JPA를 사용하여 얻을 수 있는 가장 큰 것은 SQL아닌 객체 중심으로 개발할 수 있다는 것이다. 이에 따라 당연히 생산성이 좋아지고 유지보수도 수월하다. 또한 JPA는 패러다임의 불일치도해결하였다. 예를 들면 JAVA에서는 **부모클래스**와 **자식클래스**의 관계 즉, **상속관계**가 존재하는데 데이터베이스에서는 이러한 객체의 상속관계를 지원하지 않는다(상속 기능을 지원하는 DB도 있지만 객체 상속과는 다름). 이런 상속관계를 JPA는 아래와 같은 방식으로 해결하였다.  
   
<div align="center">
![03]({{'/assets/images/posts/Project/AccountBook/post_03_003.png'| relative_url }})  
[출처 : 자바 ORM 표준 프로그래밍]
</div>
  
위의 구조에서 만약 Album 클래스를 저장한다고 가정해보자.

``` java
// Album 객체저장
jpa.persist(album);
```
  
그러면 JPA는 위의 코드를 아래의 쿼리로 변환해서 실행한다.

``` sql
INSERT INTO ITEM (ID, NAME, PRICE) .....
INSERT INTO ALBUM (ARTIST) .....
```
  
위처럼 저장하면 당연히 조회할때도 두 테이블을 엮어서 가져올 것이다. 조회하는 JAVA코드와 변환되는 쿼리를 보도록하자.  

``` java
// JAVA 코드
String albumId = "id100";
Album album = jpa.find(Album.class, albumId);
```

``` sql
// 변환된 쿼리
SELECT I.*, A.*
  FROM ITEM I
  JOIN ALBUM A ON I.ITEM_ID = A.ITEM_ID
```

위와 같이 상속관계에 대한 접근도 제공해주는데 객체지향에는 **연관관계**라는 것도 있다. 코드로 따지면 **Class에서 또 다른 Class Type을 필드 변수로 가지고 있는것**이다. 객체관계와 이를 테이블 구조로 나타낸 아래의 그림을 보도록하자.
  
<div align="center">
![03]({{'/assets/images/posts/Project/AccountBook/post_03_004.png'| relative_url }})  
[출처 : 자바 ORM 표준 프로그래밍]
</div>  
  
위의 그림은 Member 클래스가 Team 타입의 team 필드 변수를 가지고 있는 형태인데, 코드로 나타내면 아래와 같다.
  
``` java
class Member {
 String id;
 Team team;
 String username;
}


class Team {
 Long id;
 String name;
}
```
  
그렇다면 Team 객체를 참조하는 필드를 가지고 있는 Member 객체는 어떻게 저장할까?  
위에서 봤던 상속구조와 다를바가 없다. 아래의 JAVA 코드를 살펴보도록 하자.  

``` java
Member member = new Member();
member.setId("100");
member.setUsername("dbjh");

Team team = new Team();
team.setName("dev_team");

member.setTeam(team);
jpa.persist(member);
```
  
위처럼 Member 객체의 team 필드에 Team 객체를 set하고 Member 객체를 DB에 저장하게 되면 JPA는 아래와 같은 코드를데이터베이스에게 실행하라고 할것이다.
  
``` sql
INSERT INTO MEMBER (ID, TEAM_ID, USERNAME) ....
INSERT INTO TEAM (ID, NAME) ....
```
  
이렇게 저장 후  Member 객체만 조회하면, Team 객체 정보도 가져와서 Member 객체의 team필드에 주입해주기 때문에 아래와 같이 사용할 수 있다.

``` java
// JAVA 코드
Member member = jpa.find(Member.class, memberId);
Team team = member.getTeam();
```
```sql
// 변환된 쿼리
SELECT M.*, T.*
 FROM MEMBER M
 JOIN TEAM T ON M.TEAM_ID = T.TEAM_ID 
```
  
위와 같은 구조들이 더 복잡해진다고 해도 JPA는 이를 모두 지원해주기 때문에 문제없이 사용할 수 있다.  
위에서 다룬 JPA의 저장 및 조회는 아래와 같은 구조로 실행된다. 

 **- 저장 -**
  
<div align="center">
![03]({{'/assets/images/posts/Project/AccountBook/post_03_005.png'| relative_url }})  
[출처 : https://ultrakain.gitbooks.io/jpa/content/chapter1/chapter1.3.html]
</div>  

 **- 조회 -**
  
<div align="center">
![03]({{'/assets/images/posts/Project/AccountBook/post_03_006.png'| relative_url }})  
[출처 : https://ultrakain.gitbooks.io/jpa/content/chapter1/chapter1.3.html]
</div>  
  
[글의 전체적인 출처 : https://dbjh.tistory.com/77]  
  
위의 글을 대부분 이 블로그에서 가져오긴했다. 정리가 잘되어있어서 글을 읽고 또 읽으면서 정확한 개념을 잡을 수 있게 되았다.  
여기 위의 출처로 되어있는 깃북의 jpa 관련 자료는 1주일에 1번씩은 계속 읽어 봐야겠다.  
  
다음 포스팅은 JPA의 엔티티 매핑이다.