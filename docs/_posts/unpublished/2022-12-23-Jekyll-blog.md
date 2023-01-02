---
layout: single
title: This is a new article
---
Jekyll 블로그를 하는 이유

1. 다양한 테마, 커스터마이징 => 펄스널 브랜딩에 적합해보임.
2. 검색기능, 랜더링 최적화 플러그인이 있음 (jekyll-algora 등)
3. github 매뉴얼 페이지에서 소개하는 정적 블로그 프레임워크


4. 님들 나랑 블로그해요 ㅠ

5. Git 세팅하기
   1. Git 저장소를 새로 만드는 경우
   2. Git Fork
   3. gh pages 개설하기
6. Jekyll 블로그 세팅하기
   1. Jekyll 블로그 구성요소
      1. Ruby
      2. Gem
         1. Gemfile : 로컬에 설치하려는 Gem 목록. Bundle install을 해야 실제로 설치됨.
         2. Gemfile.lock : 로컬에 설치된 젬 목록. Bundle install 하면 Gemfile에서 가져옴
      3. jekyll
      4. Bundler
         1. Bundle install : Gemfile에 지정된 젬들을 로컬에 설치하는 명령어. Gemfile.lock에 내용이 추가됨.
         2. Bundle update : Gemfile.lock의 Gem들을 업데이트
   2. Ruby, Jekyll 설치(Window)
      1. [Gem + Ruby](https://jekyllrb.com/docs/installation/windows/)
      2. [Ruby란](https://www.ruby-lang.org/en/about/)
      3. Gem이란
         1. 루비 프로젝트에 포함할 수 있는 코드 조각
         2. jekyll도 ruby 코드 (gem임)
         3. [Jekyll Quick start](https://jekyllrb.com/docs/) (Gem 버전)
         4. [Jekyll Prerequisite](https://jekyllrb.com/docs/installation/)
   3. Jekyll 블로그 세팅하기
      1. Gem-based method
          1. 번들러 실행 방법
          2. 번들러가 있을 경우
             1. 번들러는 커맨드라인에서 사용 가능
             2. `bundle install bundle exec jekyll serve `
          3. 번들러를 사용하지 않는 경우(Gemfile이 없는 경우)
             1. `jekyll serve.`
             2. `https://jekyllrb.com/tutorials/using-jekyll-with-bundler/`
          4. Remote theme methodPermalink
             1. Gem file 변경이 필요하지 않아서 깃허브에 호스팅하는 블로그에 적절함.
             2. 방법
             3. Gemfile 내용 설정source `"https://rubygems.org", gem "github-pages", group: :jekyll_plugins, gem "jekyll-include-cache", group: :jekyll_plugins`
      2. Git clone으로 세팅하기
7. Git에 적용하기 

Gem-based method
사이트 테마를 로컬의 /vendor에서 가져와 적용하는 방식

Remote theme
사이트 테마를 원격 서버에서 테마를 가져와 적용하는 방식

# Gem-based method

```console

# Gemfile 생성
bundle init

# 설치하려는 Gem 명시 (_config.yml)
gem "minimal-mistakes-jekyll"

# 잼 설치
bundle install

# 번들 업데이트
bundle update
```

## Theme 재정의

로컬 루트에 `_` 접두사를 가지는 폴더가 있으면 테마 폴더를 오버라이딩합니다.
