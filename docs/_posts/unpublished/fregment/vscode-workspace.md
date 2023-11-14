
> Reference : 23-10-23 [Vscode  Reference](https://code.visualstudio.com/docs/editor/workspaces#_workspace-settings)
> OS : ubuntu 20.04 - 22.04

vscode에서 폴더를 열면 해당 디렉토리 경로가 workspace의 root 경로로 지정되며, 열린 디렉토리에서 독립적인 독립적으로 재정의 된다**

## workspace란

**workspace** 는 vscode에서 열려있는 폴더 컬렉션이다.
워크스페이스로 지정되면 각각의 폴더에 적용되는 설정값을 가진`.vscode`  디렉토리가 생성된다.

- 색인이 생성되어 문자열 검색, 파일 검색의 대상이 된다.
- 폴더별 `.vscode` 디렉토리 내용을 수정해 폴더별로 설정할 수 있다.

> `workspace`가 무조건 지정되지는 않는다. `폴더 열기` 대신 `파일 열기` 해서 사용하는 경우가 해당한다.


## multi-root

root는 workspace에 포함된 폴더들의 최상단 디렉토리를 의미한다.

워크스페이스는 여러 (루트)폴더를 가질 수 있으며 JSON 포맷으로 저장할 수 있다.

```json
# \<name\>.code-workspace
{
  "folders": [
    {
      "path": "my-folder-a"
    },
    {
      "path": "my-folder-b"
    }
  ]
}
```



## 워크스페이스 설정하기


### 권장 extension 설정하기

워크스페이스 사용시 유용한 익스텐션을 추천할 수 있음.

단일 폴더 워크스페이스의 경우 - `.vscode`에 `extensions.json` 추가

멀티 루트의 경우 - `.code-worksapce`에 `extensions.recomendations` 속성을 추가

[vscode document] (https://code.visualstudio.com/docs/editor/extension-marketplace#_workspace-recommended-extensions)