---
title: "Factory Method Pattern - 팩토리 메서드 패턴"
categories:
  - C# 
  - Design Pattern
tags:
  - C#
  - Design Pattern
  - Factory Method Pattern
sidebar:
  nav: "designPattern"
---
오늘은 생성 패턴 중 팩토리 메서드 패턴에 대해서 알아보도록 합시다.

#### Factory Method Pattern?
  
팩토리메서드 패턴은 **생설할 객체의 종류는 비슷하지만, 실제 생성할 객체가 다를 때, 객체의 생성을 약속** 하는 디자인 패턴입니다.
뼈대에서 생설할 객체의 종류를 약속하고, 객체 생성 메서드를 정의합니다.
그리고, 어떠한 인스턴스를 생성할지는 서브클래스에서 결정하게 됩니다.
  
#### UML
![UML]({{'/assets/images/posts/CS/DesignPatterns/300px-FactoryMethod.svg.png'| relative_url }})

#### 시나리오
개발자 유니크허브솔트는 SQLServer를 이용하는 A 고객사의 사용자 정보를 얻어야합니다. 그래서, SqlConnection을 이용하여 사용자 테이블의 정보를 취득하려고합니다. 기본적인 OleDB의 Connection String을 입력하고, Database를 Open 한 후에, Select Query를 이용하여, 사용자 정보를 취득하였습니다.  
  
이러던 중 B 고객사 역시 사용자 정보를 얻어야하는 상황이 발생하였습니다. 그러나 이번에는 SQLServer가 아닌, PostgreSQL을 사용하는 고객사였고, 단지 접속부와 테이블 정보만 변경된 상황에 후단 처리하는 방법은 동일하게 되었습니다.  
  
이에, 유니크허브솔트는 추가적인 데이터베이스가 오더라도 사용할 수 있도록 구조를 변경하기로 하였고, 팩토리 메서드 패턴으로 개발하기로 하였습니다.

#### 구현
이번 예제에서는 데이터베이스를 범용으로 사용하는 것이 아니라, Open -> Select -> Close -> Return 의 형식으로 단순한 Select 부분만 한다고 가정하겠습니다.  
  
먼저, C#의 OleDB 데이터베이스 관련 클래스는 모든 하나의 추상클래스로부터 파생되어 만들어집니다. 그래서 기본적인 동작에 대해서는 모두 동일하게 동작합니다.  
  
Database Open, Close, SelectUser는 추상클래스에서 선언해두고, 각 파생클래스에서 Connection String을 생성자에서 설정하도록 하겠습니다.
  
먼저 데이터베이스 타입을 선언하고,
``` cs
/// <summary>
/// 데이터베이스의 종류
/// </summary>
public enum DatabaseTypes
{
    MSSQL,
    POSTGRESQL
}
```  
  
Creator를 만들어 줍니다.
``` cs
public abstract class MyDatabase
{
    public static MyDatabase CreateFactory(DatabaseTypes databaseType)
    {
        switch (databaseType)
        {
            case DatabaseTypes.MSSQL:
                {
                    return new MSSQLDatabase();
                }
            case DatabaseTypes.POSTGRESQL:
                {
                    return new PostgreSQLDatabase();
                }
            default:
                {
                    throw new NotSupportedException("지원하지 않는 데이터베이스 타입");
                }
        }
    }
    public abstract void Open();
    public abstract void Close();
    public abstract UserInfo SelectUser();
}
```  
  
Creator를 상속받은 서브클래스를 구현합니다. 여기서는 MSSQL과 Postgresql만 구현하겠습니다.
``` cs
public class MSSQLDatabase : MyDatabase
{
    SqlConnection sqlConnection;
    public MSSQLDatabase()
    {
        string connectionString = "MSSQL CONNECTION STRING";

        sqlConnection = new SqlConnection(connectionString);
    }
    public override void Open()
    {
        sqlConnection.Open();
    }
    public override void Close()
    {
        sqlConnection.Close();
    }

    public override UserInfo SelectUser()
    {
        UserInfo userInfo = new UserInfo();
        string sq = "select * from user_info";

        SqlCommand sqlCommand = sqlConnection.CreateCommand();
        sqlCommand.CommandText = sq;

        SqlDataReader sqlDataReader = sqlCommand.ExecuteReader();

        while(sqlDataReader.Read())
        {
            userInfo.UserId = sqlDataReader.GetValue(0).ToString();
        }


        return userInfo;
    }
}
public class PostgreSQLDatabase : MyDatabase
{
    NpgsqlConnection npgSqlConnection;
    public PostgreSQLDatabase()
    {
        string connectionString = "POSTGRESQL CONNECTION STRING";

        npgSqlConnection = new NpgsqlConnection(connectionString);
    }
    public override void Open()
    {
        npgSqlConnection.Open();
    }
    public override void Close()
    {
        npgSqlConnection.Close();
    }

    public override UserInfo SelectUser()
    {
        UserInfo userInfo = new UserInfo();
        string sq = "select * from user_info";

        NpgsqlCommand npgSqlCommand = npgSqlConnection.CreateCommand();
        npgSqlCommand.CommandText = sq;

        NpgsqlDataReader npgSqlDataReader = npgSqlCommand.ExecuteReader();

        while (npgSqlDataReader.Read())
        {
            userInfo.UserId = npgSqlDataReader.GetValue(0).ToString();
        }


        return userInfo;
    }
}
```  
  
이제 객체를 생성하여 사용하보도록 하겠습니다.
``` cs
static void Main(string[] args)
{
    MyDatabase myDatabase = MyDatabase.CreateFactory(DatabaseTypes.MSSQL);

    myDatabase.SelectUser();
}
```  
  
현재 예제가 실제로 동작하기에 몇가지 부족한 면이 있지만, 팩토리 메서드 패턴을 이해하는대에는 문제가 없을 것입니다.
  
보통 라이브러리 혹은 뼈대 제작 시점에 생성할 객체의 형식을 추상 형식으로 정의하여 사용할 필요가 있을 때 팩토리 메서드 패턴을 이용합니다.
  
  
오늘은 팩토리 메서드 패턴에 대해서 알아보았습니다. 다음 시간에는 추상 팩토리 패턴에 대해서 알아보도록 하겠습니다.
### 참고문서
* [팩토리 메서드 패턴 - 위키피디아](https://ko.wikipedia.org/wiki/팩토리_메서드_패턴)