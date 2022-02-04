---
title: "Github 블로그 만들기-01"
excerpt_separator: "<!--more-->"
categories:
  - Github
tags:
  - Github
  - Github Blog
  - Blog
  - 깃허브 블로그
toc: true
toc_sticky: true
toc_label: "목차"
sidebar:
  nav: "github"

---
<style>
.portfolio-link, .portfolio-link:hover {color: #000;}
</style>

<script src="https://ajax.googleapis.com/ajax/libs/jquery/3.2.1/jquery.min.js"></script>   
<link href="//maxcdn.bootstrapcdn.com/font-awesome/4.2.0/css/font-awesome.min.css" rel="stylesheet">

## 1. Github 가입하기
당연한 얘기지만 Github Page를 사용하기에 Gtihub 가입을 먼저해야합니다.  
Github 사이트에서 회원가입을 진행하도록 합니다.
<div class="container-fluid">
  <div class="row-fluid">
    <div class="col-md-2 col-md-offset-4">
      <a href="https://github.com" class="portfolio-link" data-toggle="modal">
        <i class="fa fa-github fa-3x"></i>
        <p>Github</p>
      </a>
    </div>
  </div>
</div>
  
(repository 설정은 3번에서 할 예정)
  
  
## 2. 테마 고르기
Github Page를 완전 바닥부터 구축 할 수 있지만, 이미 다들 잘 만들어둔 테마가 있기에 테마를 사용하여 블로그를 만들기로 했습니다.  
* [http://jekyllthemes.org](http://jekyllthemes.org/)
* [https://jekyllthemes.io/free](https://jekyllthemes.io/free)
* [https://themes.jekyllrc.org/](http://themes.jekyllrc.org/)
* [https://github.com/topics/jekyll-theme](https://github.com/topics/jekyll-theme)

이중에 맘에 드는 곳에가서 내가 사용할 테마를 결정하면 됩니다.  
저는 마지막 https://github.com/topics/jekyll-theme 에서 골랐습니다.  
  
![Image]({{'/assets/images/posts/Github/014.png'| relative_url }})  
이 사이트는 인기 테마순으로 정렬해서 보여주는데, 이것저것 고민하다 그냥 처음껄로.([minimal-mistakes](https://github.com/mmistakes/minimal-mistakes))

여기서 선택해야 할 것은 그냥 빠르게 테마를 가져와 나의 블로그를 만들 것인가, 아니면 이것저것 커스텀 해서 나만의 블로그를 만들것인가를 선택해야합니다.

## 3.1 간단히 작업하기
몇개의 테마 사이트에서도 추천하는 방법으로 가장 쉬운 방법으로 블로그를 구현하는 방법입니다.  
PC에 따로 설치할 프로그램도 필요없고 그냥 Git의 Repository만 구성하게되면 바로 블로그가 동작합니다.
먼저, 해당 Git Repo로 이동 후에 우측 상단의 `fork`를 클릭하여 내 Repo로 가져옵니다.  

<p align="center">
<img src="/assets/images/posts/Github/015.png">
</p>
<p align="center">
<img src="/assets/images/posts/Github/016.png">
</p>

그리고 내 Repositories에 가보면 테마명 그대로 (여기서는 minimal-mistakes) Repositories에 추가되어 있는 것을 확인할 수 있습니다.  
그리고 프로젝의 설정으로 이동하여 Repository 이름을 `<yourusername>.github.io` 로 변경합니다.  
<p align="center">
<img src="/assets/images/posts/Github/017.png">
</p>
그리고 GemFile의 내용을 아래와 같이 변경후 Commit을 해줍니다.
  
```
source "https://rubygems.org"
gem "github-pages", group: :jekyll_plugins
gem "jekyll-include-cache", group: :jekyll_plugins
```
  
그 후 수십초 정도 후에 해당 주소로 접속해보면,
<p align="center">
<img src="/assets/images/posts/Github/018.png">
</p>
처럼 정말 기본적인 화면이 나옵니다.  
minimal-mistakes에서 제공하는 퀵가이드 문서를 보며, 원하는대로 화면을 구성하면됩니다.

## 3.2 로컬PC로 동작시키기
3.1의 방법은 간단히 블로그를 올릴 수 있으나, 포스팅 한번 뭔가 하나 수정해서 확인하려고 하면, 계속해서 `commit`을 할 수 밖에 없고, 로컬 PC에서 편집툴로 편집하더라도 결국엔 Github에서 확인할 수 밖에 없습니다.  
그래서 로컬에서 작성하고 미리 확인이 되면 그때 Github에 올리고자 Ruby를 이용한 방법을 설명하겠습니다.  
***Windows를 사용하기에 Windows를 예시로 들었습니다.**
1. 테마를 master로 다운받습니다.  
  
2. 로컬 적당한 위치에 둔 후 내 Git에 Repository로 등록합니다.  
  이때 역시 Repository 이름을 `<yourusername>.github.io` 로 변경합니다.  
  
3. Gemfile을 메모장으로 열어 아래의 내용으로 수정합니다.  
```
source "https://rubygems.org"
gem "minimal-mistakes-jekyll"
gem 'wdm',  '~> 0.1
```  
1. Windows용 Ruby를 다운받습니다.(추천버전을 다운받았습니다.) [link](https://rubyinstaller.org/)
![Image]({{'/assets/images/posts/Github/002.png'| relative_url }})   
5. 설치를 진행합니다.  
![Image]({{'/assets/images/posts/Github/003.png'| relative_url }})  
![Image]({{'/assets/images/posts/Github/004.png'| relative_url }}) 
![Image]({{'/assets/images/posts/Github/005.png'| relative_url }}) 
![Image]({{'/assets/images/posts/Github/006.png'| relative_url }}) 
![Image]({{'/assets/images/posts/Github/007.png'| relative_url }})  
6. 설치가 완료되고 나면, `RubyInstaller2 for windows` 설치 콘솔이 뜨는데,  
  여기서 1 또는 3번을 입력 후 Enter를 칩니다. (저는 3번)  
![Image]({{'/assets/images/posts/Github/008.png'| relative_url }})  
7. 설치가 완료되고 나서 윈도우의 시작에서 `Start Command Prompt with Ruby`를 찾아 실행합니다.  
8. 디렉토리 위치를 테마를 위치한 곳으로 이동합니다.  
9. 아래의 명령을 순서대로 실행합니다.  
```ruby
gem install jekyll bundler
```  
![Image]({{'/assets/images/posts/Github/013.png'| relative_url }})  
10. 
```
jekyll server
```  
를 입력하여 jekyll를 동작시킵니다.  
11. 정상적으로 동작이 완료되면 브라우저에서 [http://127.0.0.1:4000](hhttp://127.0.0.1:4000)으로 접속하면 첫 화면이 나오게 됩니다.
<p align="center">
<img src="/assets/images/posts/Github/019.png">
</p>  
  
다음시간에는 jekyll의 폴더 구조를 확인해보도록 하겠습니다.