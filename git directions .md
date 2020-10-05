git사용법
==============
git
-----------
#### 1. git & git-hub 사용절차
- Working directory(working tree) : 파일을 만들어 저장하는 공간  
  | `add`    <-> `git reset`
- Staging area : add한 파일이 모여있는곳(선별적으로 `원하는것만 커밋`하기위함)  
  | `commit`
- Repository : working tree에 변경이력이 저장되어 있는곳  
  | `push`
- git-hub 

-__staging area의 존재이유__ : 파일수정은 했자만, 최신모습으로 커밋하고 싶지않은경우를 대비하기 위함  
- cat ___ : working tree 확인  
- git status : staging area확인! (commit하기전엔 항상 git status후 commit!)

#### 2. git 파일상태
- untracked : add X
- tracked ___(tracking이란? 로컬저장소의 특정 branch가 remote저장소의 연결된 branch를 계속바라보고있는것)___
  1. __staged__ : 처음 add했거나, commit된걸 수정하고 add한 상태
  2. __unmodified__ : commit직후 working directory내부 파일
  3. __modified__ : 수정된 상태  


#### 3. 기타 command 
- git help add = man git-add   /  나갈때는 q (quit의 줄임말)
- git init : 현재 디렉토리를 working tree로 지정 및 그안에 디렉토리 생성
- git config user.name 'bomi' & user.email 'bomvll@naver.com'  



git-hub
---------------
: remote repository라 칭함
- cat readme.md : pull해온 내용을 확인
- git clone 주소 : 다른사람꺼 복사해올때


commit 다루기
-------------------
#### 1. git log : 현재까지 모든 커밋 소환(4줄형태로_ID,사용자ID,날짜,커밋메세지)
  * __git log --prety=oneline__ : (1줄 형태로 나타낸다)
#### 2. git show + ID : 각 기록에 무슨일이 있었는지 확인(tag확인가능)
#### 3. git commit => i(기록모드) => commit메세지(제목 / 내용) => 맨아래 :wq 써서 마무리
#### 4. git alias : 자주쓰는 Command에 별명을 붙임
  * git config alias.history 'log --prety=oneline' : alias.원하는별명 '원래의 Command'  
#### 5. git diff ____ ____ : Command사이에 어떤 다른점이 있는지 확인(보통 더 과거프로젝트 ID를 먼저쓴다)
#### 6. git reset : 어느시점이후로 작업이 맘에안들때 돌아간다.(Working tree도 과거의 모습으로 돌아간다)
#### 7. 최근커밋 수정법(a.k.a ctrl z와 같다)
  * 내용수정 => git add. => git commit --amend => commit message수정하여 commit
### ___Commit Message Write Method___ 
> 첫글자는 대문자, 명령조로 쓸것(Fix the code), 끝에 온점x  
> 내용은 `왜 했는지`, `어떤 문제가 있었는지`, `적용한게 어떤효과가 가는지`
#### 8. git reset
* 종류 : --soft, --mixed, --hard  
* ex) git reset --mixed ____  


> ID대신 이전 커밋건을 reset하고 싶을때  
-> git reset --soft __HEAD^__ : 이전커밋 reset  
-> git reset --hard __HEAD~2__ : 2번째 전 커밋 reset  
#### 9. git tag
* 작성법 : git tag 태그이름 ID => git tag borobom1 03ew
* git tag만 모아보기 :  git tag  
* tag내부 들여다보기 :  git show borobom1  

#### 10. git branch  
- Head : 매번 생기는 새 커밋을 가르킨다. HEAD가 가르키는 커밋에 따라 Working tree가 구성된다.  
** branch command **  
1. git branch A :  
2. git checkout A : A브랜치로 이동  
3. `git branch -b A` : A브랜치를 만들고, A로 이동!  
4. git branch -d A :  
5. git merge A : 현재 브랜치에 A의 브랜치를 가져와 합친다(현재 위치가 어디냐가 중요함)  
    - Conflict 해결법 : 충돌파일열기 => merge되었으면 하는걸로 수정 => 커밋  
    - `git merge --abort` : Merge 취소  
6. `gid add -u origin master` : master에 있는 내용을 origin remote저장소로 보낸다. (만약 origin이 없다면 만들어서라도 보낸다)   
    - -u = --set-upstream  *** 이 상태는 로컬&remote저장소가 tracking상태가 됨을 보여줌 ***  

#### 11. HEAD & Branch
    * HEAD : 최신커밋을 가르킴(Branch를 통해 간접적으로 가르킨다)
    * Branch : 코드에 대한 관리흐름

#### 12. git reset  vs git checkout
    * git reset : Branch를 움직이는 명령어
      * git reset ID : 커밋되지않은 변경사항이을 버리거나 커밋 폐기
      * git reset 파일명 : 파일 staging취소
--------------------------------------------------------------------------------
    * git checkout : HEAD를 움직이는 명령어
      * git checkout ID : 커밋 되돌리기
      * git checkout 파일명 : 워킹트리에서 수정한 내용 취소하기(복구불가)
      * git checkout 브랜치 : 브랜치로 전환

협업하기
==========================
#### 1. git pull vs git fetch
  * git fetch = remote 저장소에서 일단 가져와서 확인(HEAD가 가르키는 브랜치의 업스트림 브랜치로부터 가져옴)  
  * git pull = fetch + merge  
#### 2. git blame
  * git show로도 작성자를 알 수 있다.
#### 3. git revert 
  => 이미 push가 된 상황에서 수정이 필요할때! => 수정 후 다시 push! = git revert  
  * reset은 이전커밋으로 돌아가기때문에 remote 저장소와 버전에서 차이가 있어서 쓰면안됨  
  * revert는 revert + ID로 다시한번더 커밋하는거라 remote저장소와 연결되어 있을땐 이걸로 써야함.  
  * git revert A D : B, C, D의 커밋을 지움  

git을 자유자재로 활용하기
====================
#### Q1. reset 후 이전커밋으로 돌아갈 수 있음(언제든 왔다갔다 가능!)
  => git reflog(reference log) 돌아갈 ID가 생각나지 않을때 ID확인용으로 사용  
#### Q2. 커밋 히스토리를 보는 가장 hip한 방법
  => git log --pretty=oneline -all --graph  
#### Q3. merge vs rebase
  => merge는 새 커밋을 합쳐서 만듦  
  => rebase는 원래 있던라인처럼 한줄로 옮겨옴(history를 깔끔하게 하고싶을때!  둘다 결과는 같다)  
#### Q4. git stash(저장하다)
  => 작업중인 정보를 잠시stack에 저장  
  * 다른브랜치에서 잘못 작업한 경우?
  * 원하는 브랜치로 이동 > git stash list(현재 저장된 stash를 확인) > git stash apply ID > git add . > git commit!  
  => git stash drop ID : 삭제(apply완료된 stash는 바로바로 삭제할것)  
  => git stash pop ID : 적용과 함께 stash를 삭제함
#### Q5. git cherry-pick 
  => 원하는 커밋내용만 현재 브랜치로 가져온다
  

실제상황
====================
git push가 안될때 > git branch --set-upstream-to=origin/master master > git pull > git add . > git commit -m " " > git push  
  
  
  
  

