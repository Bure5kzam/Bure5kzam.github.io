https://www.saintsjd.com/2011/01/what-is-a-bare-git-repository/

git init 명령어로 생성된 저장소를 `working directory`라고 부른다. 저장소의 최상위 폴더에서 두 가지를 찾을 수 있다.

1. .git 디렉토리 : 저장소의 리비전 히스토리와 연관된 모든 깃이 있음
2. 워킹 트리, 프로젝트 체크아웃 파일의 복사본

git init --bare 로 생성된 저장소를 bare repo라고 부른다. 이는 `working directory`와 조금 다르게 구성된다. 

- 소스코드의 체크아웃 복사본이나 작업 중인 내용이 포함되지않음.
- 저장소의 깃 리비전 히스토리를 루트폴더에 저장함. (.git 디렉토리가 아님)

관례적으로 bare repository에는 .git 확장자를 붙임

working directory는 작업을 위해서 생성한다. 이는 실제로 파일을 수정하고, 추가하고, 삭제하고, 수정 내용을 반영하기 위해 git commit을 수행한다.만약 프로젝트의 파일을 추가, 수정, 삭제 하할 개발 머신에의 폴더 안에서 프로젝트를 시작한다면 git init을 사용해라. 주의 : 만약 git clone 한다면 .git 폴더와 워킹 파일이 있는 `working repository`가 주어진다.

bare repository 는 공유를 위해서 사용한다. 만약 개발 팀과 협업을 하고 리포지토리에 대한 수정 사항을 공유할 공간이 필요할 경우, 모든 유저들이 수정사항을 push 할 수 있는 중앙화된 장소에 베어 리포지터리를 만들고 싶을 것이다. git은 분산 버전 제어 시스템이라서 누구도 중앙 저장소의 파일을 직접 편집하지 않는다. 대신 개발자는 공유된 베어 저장소를 복제하고 저장소의 작업 복사본에서 로컬로 변경한 다음, 공유된 베어 저장소로 다시 푸시하여 변경 사항을 다른 사용자가 사용할 수 있도록 한다.

...

요약

내 개발 컴퓨터에서 로컬로 파일을 추가, 편집 및 삭제하려는 경우 git init생성 된 작업 디렉터리를 사용합니다 .git clonemyproject

준비가 되면 다른 개발자가 내 로컬 변경 사항에 액세스 할 수 있도록 로컬 변경 사항 을 git push베어 저장소 (보통 github .commyproject.git 과 같은 원격 서버 )에 공유합니다.