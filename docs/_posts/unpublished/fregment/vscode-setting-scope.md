## vscode 설정의 종류

이전 글에서 설정 기준은 크게 Default, User, Workspace가 있다고 설명했었다.

사실 더 세부적인 vs code의 설정 적용 기준들이 있다.

- 깃허브 유저
- 프로필
- 파일 확장자 및 언어
- 현재 워크스페이스
- 원격 접속 여부

[vscode 레퍼런스](https://code.visualstudio.com/docs/getstarted/settings#_language-specific-editor-settings)에서 언급되는 설정 적용 기준은 11개로, 아래로 갈수록 적용 우선순위가 높다

```bash
**Default settings** - This scope represents the default unconfigured setting values.
    # 설정이 없을 때 지정되는 기본 설정. 보통 로그인만 해도 유저 설정이 적용되기 때문에 오프라인 환경에서나 적용된다.
**User settings** - Apply globally to all VS Code instances.
    # 모든 VS code 편집기에 적용되는 유저 설정. Github 계정과 연동하여 설정값을 온라인에서 가져 올수도 있다.
**Remote settings** - Apply to a remote machine opened by a user.
    # 원격 접속시에 적용되는 설정. 리모트 머신에 vscode-server가 있어야 접속할 수 있다.
**Workspace settings** - Apply to the open folder or workspace.
    # 해당 폴더를 열었을 때만 적용되는 설정.
**Workspace Folder** settings - Apply to a specific folder of a multi-root workspace.
    # 해당 폴더를 열었을 때만 적용되는 설정 2.
**Language-specific** default settings - These are language-specific default values that can be contributed by extensions.
    # 언어별 기본 설정. 보통 언어와 관련된 확장프로그램을 설치하면 언어 설정이 추가되기 때문에 잘 사용되지 않는다.
**Language-specific** user settings - Same as User settings, but specific to a language.
    # 생략...
**Language-specific** remote settings - Same as Remote settings, but specific to a language.
    # 생략...
**Language-specific** workspace settings - Same as Workspace settings, but specific to a language.
    # 생략...
**Language-specific** workspace folder settings - Same as Workspace Folder settings, but specific to a language.
    # 생략...
**Policy settings** - Set by the system administrator, these values always override other setting values.
    # 생략...
```
