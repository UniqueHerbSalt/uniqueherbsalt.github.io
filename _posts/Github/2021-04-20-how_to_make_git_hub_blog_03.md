---
title: "Github 블로그 만들기-03(Disqus)"
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
오늘은 블로그에 댓글을 추가해보겠습니다. 네이버와 같은 블로그의 경우엔 댓글 기능이 바로 포함되어 있지만, Github Page에는 해당 기능이 없습니다.  
별도로 추가를 해줘야하는데, 무료로 사용할 수 있는 Disqus를 이용한 댓글을 추가해보도록 하겠습니다.  
  
### 1. Disqus 가입하기
[Disqus](https://disqus.com/)에 접속하여 이메일 또는 페이스북, 트위터, 구글 등의 계정을 이용하여 가입합니다.  
  
가입 후에 홈페이지에 있는 `GET STARTED`를 클릭하여 이동합니다.  
![Image]({{'/assets/images/posts/Github/021.png'| relative_url }})  

`I Want to install Disqus on my site`를 클릭합니다.  
![Image]({{'/assets/images/posts/Github/022.png'| relative_url }})  
  
웹사이트 이름과 카테고리를 선택합니다.  
![Image]({{'/assets/images/posts/Github/023.png'| relative_url }})  
  
언어 선택란에 한국어가 빠져있습니다. 이는 나중에 설정에서 변경하도록 하겠습니다. 지금은 일단 영어로 선택합니다.  
  
### 2. Disqus 설정하기
Disqus 가입은 완료 했습니다. 이제 jekyll에 적용해보도록 하겠습니다.  
_config.yml을 열고, `comments` 항목을 찾고 `provider`를 `disqus`로 `disqus`의 `shortname`을 조금전 웹사이트 이름으로 설정합니다.

``` yml
comments:
  provider               : "disqus" #(default), "disqus", "discourse", "facebook", "staticman", "utterances", "custom"
  disqus:
    shortname            : "uniqueherbsalt-blog-2"
```
  
설정 완료 후 commit과 push를 하면 포스팅 페이지 하단에 disqus가 추가된 것을 확인 할 수 있습니다.
  
### 3. Disqus 한국어 설정하기

예전에는 한국어가 리스트에 있었지만, 최근에 제외되었다고 합니다. 한국어를 사용할 수 있도록 해봅시다.  
크롬에서 disqus 사이트를 접속 후 [Home]->[사이트 Manage]->[Edit Setting]로 이동합니다.

#### 1. F12 또는 개발자도구를 누릅니다.
#### 2. 개발자 도구의 상단 [Sources]를 클릭합니다.
#### 3. 하단 Search 또는 Ctrl+Shift+F를 클릭한후 `forums/update`를 검색합니다.
#### 4. 검색된 내용을 더블 클릭한 후, `{}`를 클릭하여 보기 좋게 정렬 합니다.
![Image]({{'/assets/images/posts/Github/024.png'| relative_url }})  
#### 5. `Find`부분에 `t.useInternal ? "internal/forums/update"`을 입력하여 검색합니다.
#### 6. 검색된 부분 근처 상단(3~4줄 위) `n.props.selection.forum && (e = vm({` 이 부분에 BreakPoint를 설정합니다.
![Image]({{'/assets/images/posts/Github/025.png'| relative_url }})  
#### 7. BreakPoint가 걸린 상태에서 아무 언어나 선택 후 Save 버튼을 클릭합니다.
#### 8. 이때 우측의 e 값중 `translationLanguage`의 값을 ko로 변경후 F8을 눌러 스크립트를 진행시키면 한국어를 사용할 수 있습니다.
![Image]({{'/assets/images/posts/Github/026.png'| relative_url }})

### 4. Ruby에서 Disqus 표시하기
minimal-mistakes 테마는 기본적으로 개발모드에서는 disqus를 표시하지 않습니다. 따라서 ruby에서 `jekyll server` 명령어 전에 Production 모드로 변경 후
실행해야합니다.

```
set JEKYLL_ENV=production
jekyll server
```

이제 로컬에서도 정상적으로 disqus가 표시될 것 입니다.
