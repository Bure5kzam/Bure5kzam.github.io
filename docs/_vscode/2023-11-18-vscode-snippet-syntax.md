---
title: vscode 스니펫 문법
date: 2023-11-18 22:36
tags: [vscode, snippet]

---

> ubuntu 22.04
> vscode 1.84

> vscode reference : https://code.visualstudio.com/docs/editor/userdefinedsnippets#_snippet-syntax

body는 snippets 삽입시 실제로 들어갈 내용을 정의하는 부분이다.

이 글은 body 에서 사용할 수 있는 기능과 구문을 설명한다.

## 스니펫 기능

### Tabstops

스니펫 삽입 후 커서 위치를 이동하는 기능이다. body에서 `$1`, `$2` ...`$n` 처럼 `$` + `숫자` 형태로 존재한다.

Tabstops이 있으면 스니펫 실행 시에 커서가 해당 부분으로 이동한다. 숫자가 낮을 수록 먼저 이동하지만 예외적으로 `$0`는 마지막으로 이동할 위치다.

### Placeholders

Tabstops에 기본 값을 제공하는 기능이다. (e.g `${1:foo}` )

스니펫 삽입 시에 쉽게 수정될 수 있도록 선택된 상태로 생성된다.

중첩 구조를 가질 수 있다. (e.g `${1:anohter ${2:placeholder}}`)

### Choice

Tabstops에 기본값을 여러개 제공하는 기능이다. (e.g `${1|one,two,three|}`)

스니펫 삽입 후 커서가 placeholder에 포커싱 되고 나면 Choice가 값을 선택할 수 있도록 제공한다.

### Variables

body에서 변수를 사용하면 스니펫이 삽입될 때 지정된 내용으로 변환된다. 변수에 설정된 값이 없으면 `default` 값이나 빈 string 값이 삽입된다. 정의되지 않은 변수를 사용하면 변수 이름이 대신 들어간다.

사용할 수 있는 변수는 [링크](https://code.visualstudio.com/docs/editor/userdefinedsnippets#_snippet-syntax)의 아티클에서 Snippet syntax 섹션을 참고

### Variable transforms

변수 값을 그대로 사용하지 않고 수정해서 삽입하는 기능이다.

변수 변환을 하려면 변수 뒤에서 '/' 로 구분되는 세 부분을 추가해야 한다.

1. 정규표현식 : 삽입될 변수 값에서 정규표현식으로 매치되는 값이다. 변수 해석에 실패하면 빈 string이 된다.
2. 포맷 스트링 : 정규 표현식에서 매치 그룹을 참조하는 부분. 조건부로 값을 넣고 간단하게 수정할 수 있게 해줌.
3. 옵션 : 정규 표현식에 전달되는 플래그

다음 예는 현재 파일 이름을 확장자를 제거하고 삽입한다. 

```bash
${TM_FILENAME/(.*)\\..+$/$1/}
|           |           |  |
|           |           |  |-> 정규표현식 옵션. (m, u, g 등등 있음)
|           |           |
|           |           |->  매치 결과에서 첫 번째 캡처 그룹의 내용을 참조한다. (e.g new_file)
|           |
|           |-> 정규표현식, 변수 값이 (0개 이상의 문자 + . + 1개 이상의 문자) 로 이루어져있으면 매치한다. (e.g a.a, aa.a, )
|
|
|-> 파일이름을 나타내는 변수 (e.g new_file)
```

주의할 점으로, body 값은 쌍따움표로 감싸줘야 하기 때문에 escape charactor인 '\'를 두 번 써야 실제 적용될 때 한번으로 취급된다

### Placeholder trnasform

변수 변환 처럼 플레이스홀더에 들어가는 값도 변환을 지원한다. 삽입된 텍스트는 정규표현식으로 매칭되며, 매칭된 내용은 지정된 대체 포맷에 의해 대체돤다.플레이스 홀더 정의 마다 각자의 변환을 정의할 수 있다.

플레이스 홀더 변환 포맷은 변수 변환 포맷과 같다.

마찬가지로 스니펫 바디에서 사용하기 때문에 쌍따움표로 감싸줘야한다.

아래는 공식 문서 예시

| Example | Output 	| Explanation |
| --- | --- | --- |
|`"${TM_FILENAME/[\\.]/_/}"` 	|example-123_456-TEST.js 	|Replace the first . with _|
|`"${TM_FILENAME/[\\.-]/_/g}"` 	| example_123_456_TEST_js 	| Replace each . or - with _|
|`"${TM_FILENAME/[^0-9^a-z]//gi`}" 	|example123456TESTjs 	|Remove non-alphanumeric characters|
