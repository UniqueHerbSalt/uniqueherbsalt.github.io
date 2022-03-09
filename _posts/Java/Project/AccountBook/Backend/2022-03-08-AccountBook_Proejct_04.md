---
title: "Spring Boot 시작-JPA(Entity)"
categories:
  - Project
tags:
  - ToyProject
  - AccountBook
  - Project
sidebar:
  nav: "accountBookProject"
---
저번 포스팅에 이어서 이제 JPA의 엔티티 매핑에 대해서 알아보자.  
앞에 포스팅에서 설명을 정말 단순히 표현하자면  
  
**<center>Schema = Object</center>**
  
라고 할 수 있겠다. (물론 정확한건 아니지만!)  
일단, 스키마를 논하기전에 Entity를 먼저 설명하겠다.  


***
> ## Entity 매핑  
  
대표적인 매핑 어노테이션에는  
  
| 매핑 | 어노테이션 |
|---|---|
|객체와 테이블 매핑 | @Entity, @Table |
|기본 키 매핑	| @Id |
|필드와 컬럼 매핑	| @Column |
|연관관계 매핑	| @ManyToOne, @JoinColumn |  
  
***  
> ### Entitiy  
  
JPA를 사용해서 테이블과 매핑할 클래스는 @Entity 어노테이션을 필수로 붙여야 한다.  
(@Entity가 붙은 클래스는 JPA가 관리)  
  
#### **@Entity 적용시 주의사항**  
* 기본 생성자는 필수. (파라미터가 없는 public 또는 protected 생성자)
* final 클래스, enum, interface, inner 클래스에는 사용할 수 없다.
* 저장할 필드에 final을 사용하면 안된다.
  
``` java
public Member() {}  // 기본 생성자
```
``` java
public Member() {}  // 기본 생성자

public Member(String name) {
    this.name = name;
}
```
***  

> ### Table  
  
  엔티티와 매핑할 테이블을 지정한다.
  
``` java
@Entity
@Table(name="MEMBER")
public class Member {
    ...
}
```
***  

> ### 다양한 매핑 사용  
  
``` java
package jpabook.start;

import javax.persistence.*;  
import java.util.Date;

@Entity
@Table(name="MEMBER")
public class Member {

    @Id
    @Column(name = "ID")
    private String id;

    @Column(name = "NAME", nullable = false, length = 10) //추가
    private String username;

    private Integer age;

    @Enumerated(EnumType.STRING)
    private RoleType roleType;

    @Temporal(TemporalType.TIMESTAMP)
    private Date createdDate;

    @Temporal(TemporalType.TIMESTAMP)
    private Date lastModifiedDate;

    @Lob
    private String description;

    @Transient
    private String temp;

    //Getter, Setter

    ...
}


package jpabook.start;

public enum RoleType {
    ADMIN, USER
}
```  
  
#### **코드 설명**  
1. roleType : 자바의 enum을 사용해서 회원 타입을 구분. 자바의 enum을 사용하려면 @Enumerated 어노테이션으로 매핑.
2. createDate, lastModifiedDate : 자바의 날짜 타입은 @Temporal을 사용해서 매핑
3. description : 회원을 설명하는 필드는 길이 제한이 없다. 데이타베이스 VARCHAR 타입 대신에 CLOB 타입으로 저장. @Lob를 사용하면 CLOB, BLOB 타입을 매핑할 수 있다.


***  
  
> ### 기본키 맵핑  
  
기본적으로 데이터베이스마다 기본키 생성 방식이 다르다. 그래서 JPA에서는 DDL 명령어도 같이 이용이 가능한데, DDL에 대해서는 다음 포스팅에서 실제 소스를 만들어가면서 다시 설명하겠다.  
  
위에서 얘기했듯이 데이터베이스마다 기본키 생성이 다르다.(거의 비슷하지만) 그래서 JPA에서는 간단한 어노테이션을 이용하여 해당 필드가 기본키라고만 선언해주면된다.

``` java
@Entity
public class Member {

    @Id
    @Column(name = "ID")
    private String id;
```
  
**▶ 기본키 생성 방법**

* 기본 키를 직접 할당 : @Id만 사용
* 자동 생성 전략 사용 : @GeneratedValue 추가 및 키 생성 전략 선택.

**▶ 키 생성 전략 사용을 위한 속성 추가**

``` yaml
spring:
  jpa:
    hibernate:
      use-new-id-generator-mappings: true 
      #Hibernate 5
      
spring:
  jpa:
    hibernate:
      id:
        new_generator_mappings: true 
      #Hibernate 4.x
```
(yaml 설정은 다음 포스팅에서 확인하자)
  
  
**▶ @Id 적용 가능한 자바 타입**
* 자바 기본형
* 자바 래퍼형
* String
* java.util.Date
* java.sql.Date
* java.math.BigDecimal
* java.math.BigInteger

**기본 키 할당하는 방법**
```java
Board board = new Board();
board.setId("id1");         //기본 키 직접 할당
em.persist(board);
```
  
***  
### **▶ IDENTITY 전략**
>IDENTITY는 **기본 키 생성을 데이타베이스에 위임하는 전략**.  
>주로 MySQL, PostgreSQL, SQL Server, DB2, H2에서 사용.

* 데이터베이스에 값을 저장하고 나서야 기본 키 값을 구할 수 있을 때 사용.
* em.persist() 호출시 INSERT SQL을 즉시 데이터베이스에 전달.
* 식별자를 조회해서 엔티티의 식별자에 할당.
* 쓰기 지연이 동작하지 않는다.
  
``` java
@Entity
public class Professor {
  @Id 
  @GeneratedValue(strategy=GenerationType.IDENTITY)
  private int id;
  private String name;
  private long salary;
  ...
}
```
  
***  
### **▶ SEQUENCE 전략**
>유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트  
>주로 오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용.

#### 시퀀스 매핑 코드
``` java
@Entity
@SequenceGenerator(
    name = "BOARD_SEQ_GENERATOR",
    sequenceName = "BOARD_SEQ",
    initialValue = 1,
    allocationSize = 1)
public class Board {

    @Id
    @GeneraedValue(strategy = GenerationType.SEQUNCE,
                    generator = "BOARD_SEQ_GENERATOR")
    private Long id;
}
```
#### 시퀀스 사용 코드
``` java
private static void logic(EntityManager em) {
    Board board = new Board();
    em.persist(board);
    System.out.println("board.id = " + board.getId());
}
```
1. 먼저 데이터베이스 시퀀스를 사용해서 식별자를 조회.
2. 조회한 식별자를 엔티티에 할당.
3. 엔티티를 영속성 컨텍스트에 저장.
4. 트랜잭션 커밋.
5. 플러시 - 데이터베이스에 저장.

#### 주의
* SequenceGenerator.allocationSize 기본값이 50.  
* 반드시 1로 설정.

***
### **▶ TABLE 전략**
>키 생성 전용 테이블을 하나 만들고 여기에 이름과 값을 사용할 컬럼을 만들어 데이터베이스 시퀀스를 흉내내는 전략.  
>테이블을 사용하므로 모든 데이터베이스에 적용 할 수 있다.
  
#### TABLE 전략 키 생성 테이블
``` sql
create table MY_SEQUENCES (
    sequence_name varchar(255) not null,
    next_val bigint,
    primary key (sequence_name)
)
```
#### TABLE 전략 매핑 코드
``` java
@Entity
@TableGenerator(
    name = "BOARD_SEQ_GENERATOR",
    table = "MY_SEQUENCES",
    pkColumnValue = "BOARD_SEQ", allocationSize = 1)
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.TABLE,
                generator = "BOARD_SEQ_GENERATOR")
    private Long id;
}
```
#### TABLE 전략 매핑 사용 코드
``` java
private static void logic(EntityManger em) {
    Board board = new Board();
    em.persist(board);
    System.out.println("board.id = " + board.getId());
}


// 출력 : board.id = 1
```
* MY_SEQUENCES 테이블에 값이 없으면 JPA가 값을 INSERT
* 미리 넣어둘 필요가 없다.
**시퀀스 대신에 테이블을 사용한다는 것만 제외하면 SEQUENCE 전략과 동일**
  
***
### **▶ AUTO 전략**
>GenerationType.AUTO는 선택한 데이터베이스 방언에 따라 INDENTITY, SEQUENCE, TABLE 전략 중 하나를 자동으로 선택.
  
#### AUTO 전략 매핑 코드
``` java
@Entity
public class Board {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    ...
}
```
>@GeneratedValue.strategy의 기본값은 **AUTO.**
  
#### 장점
* 데이터베이스를 변경해도 코드를 수정할 필요가 없다.
* 키 생성 전략이 확정되지 않은 개발 초기 단계, 프로토타입 개발시 편리.
  

> ## 정리
  
### 테이블의 기본 키 선택 전략 2가지
* 자연 키
  * 비지니스에 의미가 있는 키
  * 주민등록번호, 이메일, 전화번호
* 대리 키
  * 비지니스와 관련 없는 임의로 만들어진 키, 대체 키.
  * 오라클 시퀀스, auto_increment, 키생성 테이블
  
**미래까지 충족하는 자연 키를 찾기 쉽지 않다.**  
**JPA는 모든 엔티티에 일관된 방식으로 대리 키 사용을 권장**
  
블로그에서 내용을 발췌해서 정리해보았다. (거의 복붙..)
[출처 https://ultrakain.gitbooks.io/jpa/content/chapter4/chapter4.6.html]  
  
여러가지 기본키 매핑 방법중에 작성하는 테이블 속성에 따라서 적절하게 사용하면 될듯하다.
  
다음 포스팅은 실제로 PostgreSql과 연동하여 DDL과 DML을 이용해 보도록 하겠다.