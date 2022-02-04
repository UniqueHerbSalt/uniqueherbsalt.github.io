---
title: "Github 블로그 만들기-02"
excerpt_separator: "<!--more-->"
categories:
  - Github
tags:
  - Github
  - Github Blog
  - Blog
  - 깃허브 블로그
sidebar:
  nav: "github"
---
블로그를 커스텀하기 위해서는 jekyll 프로젝트의 구조를 이해하는 것이 필요할 것입니다.  
이번 시간에는 jekyll 프로젝트의 디렉토리 구조를 확인해보도록 하겠습니다.
## jekyll 디렉토리 구조  
[jekyll 홈페이지](http://jekyllrb-ko.github.io/docs/structure/)에서 확인해보면 기본 jekyll의 폴더 구조를 확인할 수 있습니다.
```
.
├── _config.yml
├── _data
│   └── members.yml
├── _drafts
│   ├── begin-with-the-crazy-ideas.md
│   └── on-simplicity-in-technology.md
├── _includes
│   ├── footer.html
│   └── header.html
├── _layouts
│   ├── default.html
│   └── post.html
├── _posts
│   ├── 2007-10-29-why-every-programmer-should-play-nethack.md
│   └── 2009-04-26-barcamp-boston-4-roundup.md
├── _sass
│   ├── _base.scss
│   └── _layout.scss
├── _site
├── .jekyll-metadata
└── index.html # 올바른 머리말을 가진 'index.md' 도 가능
```  
이중에 커스텀 및 포스팅을 위해서 필요한 폴더 및 파일은  
`_config.yml`, `layouts`, `includes`, `_posts`, `_sass`, `index.html`

* _config.yml  
  YAML(`YAML Ain't Markup Language`)은 사람이 읽을 수 있도록 정한 데이터 직렬화 형식으로 다양한 언어에서 설정 파일로 많이 사용되고 있습니다.  
  jekyll 역시 `_config.yml`을 통해서 환경설정 정보를 관리합니다.  
  이 설정 파일의 내용은 테마 마다 내용이 다를 수 있으므로 [jekyll Config](http://jekyllrb-ko.github.io/docs/configuration/)을 통해 기본적인 내용을 파악한 후, 각 테마에서 가이드하는 문서를 참고하시기 바랍니다.  
  참고로 _cofing.yml은 `jekyll server`로 서버를 동작시킨 후 내용이 변경되어도 자동으로 Reload 되지 않습니다.  
  내용 변경 후에는 반드시 재실행 하도록 합시다.

* _layouts
  마크다운으로 작성한 글을 이쁘게 입혀주는 레이아웃을 모아둔 폴더입니다. 기본 jekyll에는 html이 몇개 없지만, 테마를 다운받은 경우 많은 레이아웃이 있을 것입니다.  
  마크다운 문서가 레이아웃을 참조하는 방식은 기본지정 방식과 포스트별 지정 방식이 있습니다.  
  이 방식은 다음 포스팅때 작성하도록 하겠습니다.  

* _includes  
  _layouts 폴더안에 `default.html` 파일의 내용을 보면 
  ![Image]({{'/assets/images/posts/Github/020.png'| relative_url }})  
  와 같이 구성되어 있고 재사용을 위해 파일을 담아두는 곳입니다.  
  기능상 `_layouts`와 `_includes` 차이점이 없어보이는데 `_layouts`은 컨텐츠 주입방식이고 `_includes` 이미 만들어진것을 재사용한다는 차이점이 있습니다.  

* _posts  
  > 한마디로 말하면, 당신의 컨텐츠다.  

  라고 jekyll 홈페이지에서는 서술하고 있습니다. 지금 보고 계시는 이 페이지 역시 _posts에 존재하는 마크다운 문서이고, 컨탠츠 보관함이라고 생각하면 됩니다.  
  다만 파일의 명명규칙은 반드시 따라야하는데
  `YEAR-MONTH-DAY-title.md` 이여야합니다. 고유주소는 포스트별로 각각 정의가 가능하지만, 날짜와 마크업 언어의 종류는 파일명과 확장자에 따라 결정 되어 집니다.
  ###### 여기서 고유주소는 /about/, /docs/ 등등 (이 페이지는 /github/ ) 포스트 별로 각각 지정 가능한 주소  

* _sass  
  css파일들의 조각입니다. 각각의 scss 파일들은 `xxx.scss`에서 (기본은 `main.scss`)에서 import 되고 최종적으로는 하나의 스타일시트 파일인 `main.css`로 가공되어 사이트에 사용가능한 스타일을 정의해줍니다.  

* index.html
  jekyll 설명에 따르면 index.html 또는 index.md 및 다른 HTML, 마크다운 파일 은 jekyll에서 머리말 섹션 
  ``` yml
  ---
  title: "title"
  ...
  ...
  ..
  ---
  ``` 
  의 형태로 이루어진 모든 파일을 찾아서 변환한다고 합니다. 이중 index.html은 첫 화면으로 사용되는 것이며, 결론적으로는 우리가 이용하는 `_posts`의 있는 파일들과 크게 다르지 않다는 것입니다.
  
지금까지 jekyll 기본 프로젝트 디렉토리 구조에 대해 알아보았습니다. 설명을 너무 간단히해서 많이 부족한것 같습니다. 다음 포스팅에서는 각 기능에 대해 좀더 자세히 다뤄보도록 하겠습니다.  
저도 처음 이용하는 jekyll 이라서 혹시나 잘못 설명하고 있는 부분에 대해서는 바로바로 지적해주시면 수정하도록 하게습니다.