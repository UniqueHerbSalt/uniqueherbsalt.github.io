---
title: "WPF의 MVVM 디자인 패턴"
categories:
  - C#/WPF
tags:
  - WPF
  - WPF MVVM Design Pattern
  - MVVM
toc: true
toc_sticky: true
toc_label: "목차"
---
C#으로 WinForm과 WPF 개발을 한지도 오래되었는데, 아직도 WPF를 기본적인 이벤트 처리 방식으로 개발하고 있었습니다. 혼자 개발하는 프로그램 위주였고, WinForm 
프로젝트를 먼저 시작한 저에겐 이 방식이 익숙할 수 밖에 없었죠  
그래서 최근에 MVVM 디자인패턴을 사용하기 시작했습니다. (진즉할걸 ㅡㅡ)  
자료 정리겸 포스팅을 시작합니다.

###### MVVM?  
MVVM은 `Model`, `View Model`, `View`로 이루어진 디자인패턴 입니다.  
 
`Model`은 `Entity` 또는 `Domain`의 개념으로 데이터를 나타내는 기본 단위입니다.  
`View`는 화면에 표시되는 부분으로 WPF에서는 윈도우 입니다.  
`ViewModel`은 `View`와 `Model` 사이의 연결 요소로 MVVM 패턴의 가장 핵심적인 역할을 수행합니다. UI의 로직, 커맨드, 이벤트 등에 대한 참조가 포함되어있고, `Data Binding`을 통해 `View`에 표시되는 데이터를 갱신하게 됩니다.  
이때 `View`가 가지고있는 `Observer`가 `ViewModel`을 보고 있으면서 데이터가 변경되면 스스로 UI를 갱신하게 됩니다.
UI를 갱신할 때, `ViewModel`은 `INotifyPropertyChanged`를 상속받으며, `ProperryChanged` 이벤트를 발생 시켜야합니다. 

다소 복잡해보이는 구조입니다. 하지만, 웹 프로젝트의 `Frontend`-`Backend` 구조(디자인패턴과 다른 이야기이지만 구조 개념상)나 Spring Framework의 MVC 디자인패턴과 같이 궁긍적인 목적은 `SoC(Separation of Concerns)`, 관심사분리가 가장 크다고 볼수 있습니다.  

모듈별로 정확히 나뉘어 있을 경우 서로에게 주는 영향도가 낮기에 디자인패턴을 사용하는 것입니다.

![Image]({{'/assets/images/posts/wpf/001.png'| relative_url }}) 

물론 MVVM 디자인패턴의 장단점은 있습니다.  

### 장점
1. Model과 View간의 의존성이 분리되었다.
2. 하나의 ViewModel을 설계해서 다양한 뷰에 적용할 수 있다.  
  즉, 비슷한 기능이 있다면 ViewModel을 같이 사용할 수 있다는 의미다.  

### 단점
1. 기존 Window Form 개발자라면 다소 생소한 구조에 기술 학습시간이 많이 소요된다.
2. 디자이너가 없는 혼자하는 프로젝트이거나 작은 프로젝트라면 다시 한번 생각을..~~(이건 나네)~~
3. 정말 정확한 구조화를 하지 않는다면, 지옥을 보게 됩니다....(이건 지극히 제 생각)

오늘은 MVVM 디자인패턴에 대해서 알아보았습니다. 다음 포스팅은 예제를 통해서 Event-driven방식과 WVVM의 차이를 보겠습니다.

<!-- 15년에 C#을 시작했는데 그때부터 MVVM 패턴으로 했었다면...  
개인적으로 WPF는 반드시 MVVM으로 해야해는 아닙니다. 정말 간단한 프로그램이고 동작이 복잡하지 않고 UI가 그렇게 중요하지 않다면 필요는 없겠죠 -->