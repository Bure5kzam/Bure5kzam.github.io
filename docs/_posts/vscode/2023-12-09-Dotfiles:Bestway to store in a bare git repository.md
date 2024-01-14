---
title: dotfile 설정 동기화 환경 만들기
category: vscode
published : False
---

github로 dotfile 환경을 업로드하고 bash 기반으로 다운로드, 실행하는 방법에 관한 글

> 아틀라스 사의 매뉴얼 번역 및 설명을 추가한 글입니다.
>
> https://www.atlassian.com/git/tutorials/dotfiles


## 로컬에 dotfile 관리용 git 저장소 생성하기

```bash
cd $HOME

git init --bare $HOME/.cfg
# 깃 베어 저장소를 ~/.cfg를 생성

alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'
# 일반 git 대신 설정 저장소와 상호작용할 때 사용할 명령어를 생성

config config --local status.showUntrackedFiles no
# -local 플래그를 추가해서 우리가 정확히 트래킹하지 않는 파일들을 숨긴다. 
# config status와 다른 명령어를 입력할 때, 관심없는 파일들은 untracted 되어 보이지 않게 될 것이다.
```

`alias` 로 지정한 별칭은 새로 쉘을 생성하면 사라진다.

새로 쉘을 생성할 때마다 config 사용하려면 방금 추가한 정의를 .bash 추가한다.

```bash
echo "alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'" >> $HOME/.bashrc
```

### git bare의 역할과 목적

일반적으로 로컬 저장소를 생성하면 깃 정보를 기록하는 `.git` 폴더가 소스코드를 보관하는 워크 디렉토리 안에 생성된다. 반면 bare 저장소를 사용하면 워크트리 지정 없이 .git 디렉토리만 생성된다.

이 예시에서는 워크 디렉토리의 경로와 깃 폴더 경로를 다르게 지정한다.

아마도 다양한 폴더를 보관하는 홈 디렉토리 특성상 `.git`이 홈에 있으면 순환 참조 문제가 발생할 수 있기 때문에 이를 피하려는 의도로 옮긴 것이 아닐까 싶다.

워크트리 경로가 없기 때문에 git 명령어를 사용어를 사용할 때마다 `--git-dir`, `--work-tree` 옵션으로 경로를 지정해줘야 한다.

### git config에서 사용한 옵션의 의미

`--local` 은 설정 내용을 현재 로컬 저장소에만 적용하는 옵션이다. 기본 적용 옵션이다.

`status.showUntrackedFiles`

기본적으로 git-status나 git-commit는 트래킹 되지 않는 파일들도 보여준다.

이러면 doefile 저장소를 구성할 때 home에 있는 모든 파일들이 보이므로 해당 옵션으로 숨겨준다.

---

## dotfile 저장소 구성

홈 디렉토리에 dotfiles를 구성한 뒤,워크트리 경로와 .git 경로를 지정한 git 명령어 별칭 `config`으로 커밋을 추가한다.

```bash
config status
config add .bashrc
config commit -m "Add bashrc"
config push
```

구성한 내용을 다른 곳에서 사용하려면 원격 저장소에 수정 내용을 반영해야 한다.

이를 위한 원격 저장소 경로 등록 과정은 다루지 않는다.

## dotfiles 구성을 다른 시스템에서 불러오기

dotfile을 구성한 원격 저장소를 불러와 불러와 구성한다.

```bash

cd $HOME

alias config='/usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME'

echo ".cfg" >> .gitignore

git clone --bare <git-repo-url> $HOME/.cfg

config checkout

config config --local status.showUntrackedFiles no
```

새로운 시스템에 관리 중인 파일과 같은 이름의 파일이 있으면 충돌이 발생할 수 있다.

```bash
error: The following untracked working tree files would be overwritten by checkout:
    .bashrc
    .gitignore
Please move or remove them before you can switch branches.
Aborting
```

필요하다면 백업하고, 필요없으면 지지운다.

```bash
mkdir -p .config-backup && \
config checkout 2>&1 | egrep "\s+\." | awk {'print $1'} | \
xargs -I{} mv {} .config-backup/{}
```

### 인자 없는 git checkout

.git 폴더에 있는 저장소의 수정 내역을 기반으로 워크트리에 실제 파일을 구성한다.

만약 에러가 발생하면 리다이렉션을 이용해 에러를 출력으로 내보내며, 파이프라인으로 egrep의 입력으로 전달한다.

### egrep과 awk

egrep은 오류 메세지에서 정규 표현식 매치되는 줄만 stdout으로 전달한다.

`\s` : 스페이스, 공백에 매치됨.
`+` : 반복자. 앞의 내용이 1개 이상이면 모두 매치됨. 여기선 1개 이상의 공백에 매치됨.
'\.' : 콤마에 매치됨.

awk는 줄을 구분자로 나눠서 n번째 문자열을 추려내는데 사용한다.

여기서는 기분 구분자인 공백으로 나눴을 때 첫 번째 문자열을 출력한다.

```bash
error: The following untracked working tree files would be overwritten by checkout:
    .bashrc
    .gitignore
Please move or remove them before you can switch branches.
Aborting
```

### xargs

xargs는 stdout의 내용을 활용해 다른 명령어를 실행할 때 사용하는 명령어다.

파이프라인은 출력 내용을 다음 명령어를 stdin으로 제공하는 기능뿐인 반면에 xargs는 새로운 커맨드의 인자로 사용하는 등 다양한 활용이 가능하다.

-I는 명령어를 실행할 때 치환할 문자를 지정하는 옵션으로, 다음 명령어를 시작할 때 치환 문자를 stdin 내용 으로 변경한다.

만약 `.bashrc`가 중복되면 ``

`mv .bashrc .config-backup/bash.rc`

로 치환되어 실행된다.


총 정리하면 다음과 같다.

```bash
#!/bin/bash

# URL_GIT_DOTFILE_REPO 변수에 저장된 값 가져오기
REPO_URL=${URL_GIT_DOTFILE_REPO:-https://github.com/Bure5kzam/dotfile.git}
TODAY=$(DATE +%Y-%m-%d_%H-%M-%S)
cd $HOME

# 이미 .cfg 디렉토리가 존재하는지 확인
if [ -d "$HOME/.cfg" ]; then
  echo ".cfg directory already exists, backing up to .config-backup"
  
  # 중복된 파일 백업 폴더 생성
  mkdir -p $HOME/.config-backup
  
  # 기존 .cfg 디렉토리를 백업 디렉토리로 이동
  mv $HOME/.cfg $HOME/.config-backup/.cfg_backup_$TODAY
fi

# dotfile 저장소 구성
git clone --bare "$REPO_URL" $HOME/.cfg || { echo "Clone failed"; exit 1; }

# config 명령어 생성
function config {
   /usr/bin/git --git-dir=$HOME/.cfg/ --work-tree=$HOME $@
}

# dotfile 설정 적용
config checkout

# 실행했을 때 에러가 났으면 백업 프로세스 실행
if [ $? = 0 ]; then
  echo "Checked out config."
else
  echo "Backing up pre-existing dot files."
  BACKUP_DIR=dotfiles_backup
  # 중복된 파일 백업 폴더 생성
  mkdir -p $HOME/$BACKUP_DIR/$TODAY
  
  # 중복된 파일을 .config-backup으로 이동
  config checkout 2>&1 | egrep "^\s+.*" | awk {'print $1'} | awk -F '/' {' print $1 '} | xargs -I{} mv {} $HOME/$BACKUP_DIR/$TODAY/{}
fi

config checkout
config config status.showUntrackedFiles no


```

쉘 환경변수 `URL_GIT_DOTFILE_REPO` 에 자신의 dotfile 저장소를 저장해두면 된다.

---

## 로컬에 dotfile 관리용 git 저장소 생성하기(curl)

dotfile 저장소의 다른 브랜치에 스크립트 파일만 모아두었다.

```bash
curl -Lks https://raw.githubusercontent.com/Bure5kzam/dotfile/script/dotfiles/set_to_local.sh | /bin/bash
```
