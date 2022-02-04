---
title: "Abstract Factory Pattern - 추상 팩토리 패턴"
categories:
  - C# 
  - Design Pattern
tags:
  - C#
  - Design Pattern
  - Abstract Factory Pattern
sidebar:
  nav: "designPattern"
---
오늘은 생성 패턴 중 추상 팩토리 패턴에 대해서 알아보도록 합시다.

#### Abstract Factory Pattern?
  
추상 팩토리 패턴은 **팩토리 객체를 통하여 호환성 잇는 객첵군을 생성** 하는 디자인 패턴입니다.
서로 관련이 있는 객체들을 통째로 묶어서 팩토리 클래스로 만들고, 이들 팩토리를 조건에 따라 생성하도록 다시 팩토리를 만들어서 생성하는 패턴입니다.
  
#### UML
![UML]({{'/assets/images/posts/CS/DesignPatterns/677px-Abstract_factory_UML.svg.png'| relative_url }})

#### 시나리오
(이번 예제는 위키백과에 있는 내용을 토대로 작성하였습니다.)  
개발자 유니크허브솔트는 Form에 들어갈 버튼을 커스텀하려고합니다. 근데 이 프로그램은 윈도우와 MacOS에서 동작하는 방식이 다르게 되어있습니다. 단순히 OS를 구분하여 분기를 통해서 버튼의 기능을 다르게 하려고 했는데, 이번에 Linux까지 추가된다고 합니다. 그래서 Button과 Button을 생성하는 Factory를 이용하여 추상 팩토리 패턴으로 개발하기로 하였습니다.

#### 구현
Button의 인터페이스를 선언합니다. 
``` cs
interface IButton
{
    void Paint();
}
```
위에서 분명 Abstract, 추상 이라고 표현하고 왜 interface라고 적었을까요? 개발 언어의 interface와 abstract는 명확하게 사용하는 기능이 다르지만 디자인패턴에서의 interface는 단순히 중간에 연결부분 이라고 해석하기에 이것을 반드시 `abstract로 해야한다.` `intercace로 해야한다.`라는 의미는 아닙니다. 
  
Factory 인터페이스를 선언합니다.
``` cs
interface IGUIFactory
{
    IButton CreateButton();
}
```  
  
윈도우 OS에서 사용될 Factory를 작성합니다.
``` cs
class WinFactory : IGUIFactory
{
    public IButton CreateButton()
    {
        return new WinButton();
    }
}
```  

Mac OS에서 사용될 Factory를 작성합니다.
``` cs
class OSXFactory : IGUIFactory
{
    public IButton CreateButton()
    {
        return new OSXButton();
    }
}
```  

각 OS의 Button의 기능을 구현하고
``` cs
class WinButton : IButton
{
    public void Paint()
    {
        //Render a button in a Windows style
    }
}
```  

``` cs
class OSXButton : IButton
{
    public void Paint()
    {
        //Render a button in a Mac OS X style
    }
}
```  

  
이제 객체를 생성하여 사용하보도록 하겠습니다.
``` cs
class Program
{
    static void Main()
    {
        var appearance = Settings.Appearance;

        IGUIFactory factory;
        switch (appearance)
        {
            case Appearance.Win:
                factory = new WinFactory();
                break;
            case Appearance.OSX:
                factory = new OSXFactory();
                break;
            default:
                throw new System.NotImplementedException();
        }

        var button = factory.CreateButton();
        button.Paint();
    }
}
```

  
위키백과의 예제에서는 단순히 Button과 OSFactory로만 구현되어있지만, OS에 따라 추가적으로 붙는 기능이 다르거나 하는 경우에 추상 팩토리 패턴이 유용합니다.
  
  
오늘은 추상 팩토리 패턴에 대해서 알아보았습니다. 다음 시간에는 빌더 패턴에 대해 알아보도록 하겠습니다.
### 참고문서
* [추상 팩토리 패턴 - 위키피디아](https://ko.wikipedia.org/wiki/프로토타입_패턴)