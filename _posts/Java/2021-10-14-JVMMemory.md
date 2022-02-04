---
title: "Java JVM"
categories:
  - Java
tags:
  - JVM
---
Java의 근간이라고 말할 수 있는 JVM에 대해서 알아보도록 하겠습니다.    
  
### 1. JVM?  
Java Virtual Machine(JVM)은 자바 바이트코드(.class)를 실행하는 프로그램입니다. 즉, 자바 프로그램이 실행할 수 있는 런타임 환경을 제공합니다. 흔히 우리가 알고있는 JRE(Java Runtime Enviroment)가 JVM 의 구현체를 가지고 있습니다.  
  
자바 컴파일러(javac)에 의해 생성된 자바 바이트 코드는 기계어가 아니기 때문에 OS에서 바로 실행할 수 가 없습니다. JVM은 OS가 자바 바이트코드를 이해 할 수 있도록 해석해줍니다.  
JVM의 해석을 거치기 때문에 C언어와 같은 네이티브언어에 비해 느렸지만 JIT(Just-In-Time) Compiler를 구현해 해당 문제를 극복했습니다.  
  
Byte Code는 JVM 위에서 OS 상관없이 실행됩니다. 이런 점이 Java의 가장 큰 장점이라고 할 수 있습니다.  
OS에 종석적이지 않고, Java 파일 하나만 만들면 어느 디바이스든 JVM 위에서 실행 할 수 있습니다.  
  
### 2. JVA의 구조  
  
JVM은 시스템으로부터 프로그램을 수행하는데 필요한 메모리를 할당받고 JVM은 이 메모리를 용도에 따라 여러 영역으로 나누어 관리합니다.  

JVM은 크게 세 가지의 구성으로 이루어 집니다.
![UML]({{'/assets/images/posts/Java/JVM.png'| relative_url }})  
  

### 3. JVM의 구성 요소  
구성 요소들을 다음과 같습니다.
- Class Loader
- Runtime Data Area
- Execution Engine
- Java Native Interface
- Java Native Libraries
  
##### 3.1 Java Class Loader  
클래스를 메모리에 올리는 동적 클래스 로딩 기능은 ClassLoader에 의해 이루어집니다.  
  
즉, 자바 클래스들은 한번에 메모리에 올라오지 않고, 어플리케이션에 의해 필요할 때 동적으로 메모리에 올라오게 됩니다.  
클래스 로딩은 3단계로 이루어집니다.  
> Loading -> Linking -> Initialization  
  
###### 1) Loading  
모든 클래스들이 하나의 클래스 로더에 의해 로딩되지 않습니다. 클래스의 종류와 경로에 따라 어떤 클래스로더에 의해 로드될지 결정됩니다.  
만약, 클래스가 발견되지 않는다면 NoClassDefFoundError와 ClassNotFoundException이 발생하게 됩니다.  
  - BootStarp ClassLoader
    - 부트스트랩 클래스로더는 3가지 기본 클래스로더 중 최상위 클래스로더로서, 쉽게 말하면 jre/lib/rt.jar에 담긴 JDK 클래스 파일을 로딩한다.
    - Native C로 구현돼 있어서, String.class.getClassLoader()는 그냥 null을 반환한다. Primordial ClassLoader 라고 불리기도 한다.  
  
  - Extension ClassLoader
    - 익스텐션 클래스로더는 jre/lib/ext 폴더나 java.ext.dirs 환경 변수로 지정된 폴더에 있는 클래스 파일을 로딩한다.
    - Java로 구현되어 있으며 sun.misc.Launcher 클래스 안에 static 클래스로 구현되어 있으며, URLClassLoader를 상속하고 있다.
  
  - Application ClassLoader
    - 애플리케이션 클래스로더는 -classpath(또는 -cp)나 JAR 파일 안에 있는 Manifest 파일의 Class-Path 속성값으로 지정된 폴더에 있는 클래스를 로딩한다.
    - 익스텐션 클래스로더와 마찬가지로 Java로 구현되어 있으며, sun.misc.Launcher 클래스 안에 static 클래스로 구현되어 있으며, URLClassLoader를 상속하고 있다.
    - 개발자가 애플리케이션 구동을 위해 직접 작성한 대부분의 클래스는 이 애플리케이션 클래스로더에 의해 로딩된다.  
  
###### 2) Linking  

  1. Verify  
    ByteCode Verifier는 생성된 자바 바이트 코드가 적절한지 검증합니다.
  2. Prepare  
    모든 static 변수의 메모리가 할당되어지고 default 값들이 할당됩니다.
  3. Resolve  
    모든 Symbolic memory reference가 Method Area에 있는 original references로 대체됩니다.
  
###### 3) Initialization
Class Loading의 마지막 단계입니다. 모든 Static 변수들이 Original 값들로 할당되고 static block이 실행됩니다.
```java
  static class Student {
    // Static Block
    static {
      System.out.println("static...1");
    }
  }
```  
    
##### 3.2 Runtime Data Areas  
Runtime Data Area는 메모리에 올라와있는 자바 프로그램입니다. 크게 5개의 주요 구성요소로 나뉘어져있습니다.
  - Method Area
     static 변수들을 포함해서 클래스 수준의 모든 데이터가 이곳에 저장될 것입니다. JVM마다 오직 하나의 method area가 있으며 공유되는 리소스들입니다. 
  - Heap Area
     모든 Objects와 그에 상응하는 instance 변수 그리고 배열들이 이곳에 저장됩니다. 물론 JVM마다 하나의 Heap Area가 존재합니다. Method areas 와 Heap areas는 여러 스레드들간에 공유되는 메모리입니다. 즉, 이 곳에 저장된 데이터들은 스레드에 안전하지 않습니다.
  - Stack Area
     각각의 스레드를 위해 분리된 런타임 스택이 생성됩니다. 메소드 호출마다, Stack Frame이라 불리는 하나의 Entry가 Stack memory가 생성됩니다. stack area는 스레드에 안전하지만 공유되는 리소스가 아닙니다. 또, Stack Frame은 3가지 Subentities로 나뉘어집니다. 
     - Local Variable Array
     - Operand stack
     - Frame data
  - PC Register 
     각 스레드는 현재 실행중인 상태 정보를 저장하는 개별적인 PC Register들을 가질 것이고 진행이 되면 지속적으로 갱신됩니다.
  - Native Method stacks
     자바 이외의 언어로 된 코드를 위한 스택이다. JNI(Java Native Interface)를 통해 호출되는 다른 언어의 코드를 수행하기 위해 존재합니다.

##### 3.3 Execution Engine
Runtime Data Area에 할당된 Java ByteCode를 Execution Engine이 실행시킵니다.
The Execution Engine 은 bytecode 를 읽고 한줄 한줄 실행시킵니다.
  - Interpreter
     interpreter는 bytecode를 빠르게 해석하지만 느리게 실행한다. interpreter의 단점은 하나의 메소드를 여러번 호출할 때, 매번 새로운 해석이 요구된다는 것이다.
  - JIT Compiler
     JIT Compiler는 interpreter의 단점을 없애준다. Execution Engine은 interpreter가 bytecode를 변환하는데 사용하지만 반복되는 코드를 발견했을 때 전체 bytecode를 컴파일하고 그것을 native code로 바꾸는 JIT compiler를 사용한다. 여기서 생성된 native code는 반복되는 메소드 호출에 대해 성능을 개선하기위해 직접적으로 사용된다.
  - Garbage Collection
     참조되어 있지 않는 Objects들을 모으고 제거하는 역할을 한다. Garbage Collection은 System.gc() 를 호출하므로 발생한다. 하지만 실행이 보장되지는 않는다.
  
##### 3.4 Java Native Interface (JNI)
JNI 는 Native Method Libraries와 상화작용하며 Execution Engin을 위해 Native Libraries를 제공합니다.
  
##### 3.5 Native Method Libraries
Execution Engine에서 필요한 Native Libraries의 모음입니다.
  
[Runtime Data Areas와 Garbase Collection에 대해서는 추가적으로 다시 설명할 예정입니다.]
  
오늘은 JVM의 구조에 대해서 알아보았습니다. 우리가 자바를 사용하면서 JVM에 대해서 한번쯤은 깊숙하게 생각해 볼 필요가 있다고 생각해서 포스팅을 해보았습니다.  
다음 시간에는 Runtime Data Areas에 실제로 메모리가 어떻게 할당되고 GC는 이를 어떻게 처리할지에 대해 알아보도록 하겠습니다.