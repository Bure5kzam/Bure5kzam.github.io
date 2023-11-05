
> Reference : 23-10-23 [Vscode  Reference](https://code.visualstudio.com/docs/editor/workspaces#_workspace-settings)
> OS : ubuntu 20.04 - 22.04

vscode를 사용했다면 폴더를 열어서 포함된 파일들을 열고 수정한 경험이 있겠지만, **열린 디렉토리 경로가 workspace의 root 경로로 지정된다는 것**과, **열린 디렉토리에서 vscode 설정이 독립적으로 재정의 된다는 것**은 모를수도 있다.

## workspace란

vscode에서 현재 열려있는 폴더를 **workspace**라고 한다.

폴더가 **workspace**의 **root**로 지정될 경우,
- 색인이 생성되어 문자열 검색, 파일 검색의 대상이 된다.
- 워크스페이스용 설정 재정의 폴더인`.vscode`가 루트에 생성된다.

**workspace**가 없이도 문서를 편집할 수 있으며, `폴더 열기` 대신 `파일 열기` 해서 사용하는 경우가 해당한다.


## multi-root

워크스페이스는 동시에 여러 폴더를 열 수 있으며, 단일 폴더만 열려 있는 워크스페이스를 `single folder workspace`, 다중 폴더를 `multi root workspace`라고 한다






https://code.visualstudio.com/docs/editor/extension-marketplace#_workspace-recommended-extensions

- 멀티루트
- 