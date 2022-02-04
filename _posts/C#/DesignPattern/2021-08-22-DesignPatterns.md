---
title: "C# 디자인 패턴"
categories:
  - C# 
  - Design Pattern
tags:
  - C#
  - Design Pattern
toc_sticky: true
toc_label: "목차"
sidebar:
  nav: "designPattern"
---
GoF의 디자인 패턴을 정리해보려고 합니다. 

일단 디자인 패턴은 왜 사용할 까요? 그동안 개인적으로 정리한 내용을 적자면,
1. 다른 개발자나 개발팀과의 의사소통에 있어 디자인 패턴을 사용하는 것은 수많은 주석이나 문서보다 강력하다.
2. 코드의 재사용율을 높이고, 결합도를 낮추거나 OCP와 같은 규칙을 지킵으로써 유지보수를 간결하게 한다.
  
기존 개발시 단독으로 개발하는 경우가 많아서 디자인 패턴보다는 익숙한 형태의 코드를 사용하다보니, 다른 개발자에게 해당 코드를 인계할 때, 너무나 많은 주석과 문서가 필요했습니다.
좀 더 체계적이고 개발자스러운(?) 코드를 위해 디자인 패턴을 학습하고 이를 실제 코드에 반영해 보려고합니다.

GoF(Gang of Four)에서는 23가지 디자인 패턴을 3가지의 유형으로 분류합니다. 

##### 1. Creational Design Pattern
생성패턴은 객체의 생성과 관련된 패턴으로 객체의 생성에 대한 절차를 추상화하는 패턴입니다.
객체를 생성-합성 하는 방법 / 객체의 표현방법과 시스템을 분리합니다.
  
###### 생성패턴의 특징
생성패턴은 시스템이 어떤 구체적인 클래스를 사용하는지에 대한 정보를 캡슐화합니다.
생성패턴은 이들 클래스의 인스턴스들이 어떻게 만들고 어떻게 서로 맞붙는지에 대한 부분을 완전히 가립니다.
즉, 객체의 생성과 조합을 캡슐화해 특정 객체가 생성되거나 변경되어도 프로그램 구조에 영향을 크게 받지 않도록 유연성을 제공합니다.
  
###### 생성패턴의 종류
- Factory Pattern
- Abstract Factory Pattern
- Builder Pattern
- Prototype Pattern
- Singleton Pattern
  

##### 2. Structural Design Pattern
구조패턴은 클래스나 객체를 조합해 더 큰 구조를 만드는 패턴입니다.
예를 들어 서로 다른 인터페이스를 지닌 2개의 객체를 묶어 단일 인터페이스를 제공하거나 객체들을 서로 묶어 새로운 기능을 제공하는 패턴입니다.
  
###### 구조패턴의 특징
서로 독립적으로 개발한 클래스 라이브러리를 마치 하나인 것처럼 사용할 수 있습니다.
여러 인터페이스를 합성하여 서로 다른 인터페이스들의 통일된 추상을 제공합니다.
인터페이스나 구현을 복합하는 것이 아니라 객체를 합성하는 방법을 제공합니다.
  
###### 구조패턴의 종류
- Adapter Pattern
- Bridge Pattern
- Composite Pattern
- Decorator Pattern
- Facade Pattern
- Flywight Pattern
- Proxy Pattern
  

##### 3. Behavior Design Pattern
행동패턴은 객체나 클래스 사이의 알고리즘이나 책임 분배에 관련된 패턴입니다.
한 객체가 수행할 수 없는 작업을 여러 개의 객체로 어떻게 분배하며 객체 사이의 결합도 최소화에 중점을 두며,
패턴을 주로 클래스에 적용하는지 아니면 객체에 적용하는지에 따라 구분되는 패턴입니다.
  
###### 행동패턴의 특징
상속보다는 복합을 통해서 객체 사이에 행동 처리의 책임을 분산시킵니다.
즉, 하나의 객체가 스스로 모든 처리를 하는 것이 아니라, 관련된 객체들이 하나의 처리를 책임지는 방법입니다.
  
###### 행동패턴의 종류
- Chain of responsibility Pattern
- Command Pattern
- Interpreter Pattern
- Iterator Pattern
- Mediator Pattern
- Template Mehtod Pattern
- Visitor Pattern
- Memonto Pattern
- Observer Pattern
- State Pattern
- Strategy Pattern
  
  
이번 포스팅에는 패턴의 종류를 확인해 보았고, 다음 포스팅부터 생성, 구조, 행동 패턴의 각각의 패턴들을 알아보고 예제를 통해서 각 패턴의 특징을 알아보도록 하겠습니다.

<hr/>
**포스팅에 활용될 예제나 내용에 대한 인용을 허락해 주신 「언제나 휴일」 님께 진심으로 감사의 말씀 드립니다.**

[언제나 휴일(YouTube)](https://www.youtube.com/channel/UCudvMbbxHnAdPlM1sa-VHxQ)