---
title: vscode 스니펫 만들기 (c++)
date: 2023-11-17 22:36
tags: [snippet]

---

> ubuntu 22.04
> vscode 1.84

> vscode reference : https://code.visualstudio.com/docs/editor/userdefinedsnippets

vscode 자동완성 만들기

스니펫 생성 커맨드를 실행한다 (ctrl + shift + p)

사용하려는 언어로 c++을 선택하면 홈의 `/.config/Code/User/snippets` 경로에 `cpp.json`가 생성된다.

경로를 보니 호스트 유저에서 실행하는 vscode에 모두 적용되는 설정으로 추측된다.

```json
{
// Place your snippets for cpp here. Each snippet is defined under a snippet name and has a prefix, body and 
// description. The prefix is what is used to trigger the snippet and the body will be expanded and inserted. Possible variables are:
// $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. Placeholders with the 
// same ids are connected.
// Example:
    
    "Print to console": {
    // 스니펫 이름. 기본적으로 스니펫 호버창에서 description을 보여주나 사용할 수 없을 경우 이름을 보여줌
        "prefix": "log",
        // 스니펫 인텔리젠스를 동작시키는 트리거 단어. 접두사 검색도 지원한다.
        "body": [
        // 실제 삽입되는 내용
        // placeholder(e.g $1, $2)는 body 삽입시 커서가 이동할 수 있는 위치다. tab을 누르면 다음 placeholder로 이동할 수 있다.
        // ${1:array} 로 표현하면 기본값이 array가 된다.
            "console.log('$1');",
            "$2"
        ],
        "description": "Log output to console"
    }
}
```

예시 파일에서 주석을 해제 후 리로드해서 아무 cpp 파일에서 log를 실행해보니 동작한다

![vscode 스니펫 추가하기]({{ "/assets/image/vscode-snippet-c++-test.png" | relative_url }}) {: .full}
