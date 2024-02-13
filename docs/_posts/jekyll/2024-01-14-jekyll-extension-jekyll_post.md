---
layout : single
title: Jekyll, vscode extension, jekyll-post 기본 템플릿 설정하기
category : jekyll
# author: User
# summary: Summary of the article
---

vscode에는 jekyll 포스트를 생성하는 확장프로그램 jekyll-post가 있다. (extension id : rohgarg.jekyll-post)

지정한 파일 이름에 날짜를 추가 해주고 내부에 기본 템플릿을 생성해줘서 유용하게 사용하고 있다.

> vscode, market place, docs : https://marketplace.visualstudio.com/items?itemName=rohgarg.jekyll-post

## 기본 파일 내부 템플릿 설정하기 

vscode에서 열러는 폴더/워크스페이스의 루트 경로에 `.post-template` 파일을 만들어주면 설정이 가능하다.

```jekyll
---
layout: single
title: This is a new article
author: User
summary: Summary of the article
---
```

주석처리도 가능하다.

jekyll에서는 파일에 적용될 Form matter 속성을 _config.yml에서 폴더별로 미리 정의해둘 수 있어서 자주 수정될 속성만 주석으로 달아두면 유용할 것 같다.

```jekyll
---
# layout: posts
title: hello
# author: User
# summary: Summary of the article
---

```

하지만 아쉽게도 `.post-template`을 정의하면 날짜를 자동으로 채워주는 기능이 사라진다는 단점이 있다.


