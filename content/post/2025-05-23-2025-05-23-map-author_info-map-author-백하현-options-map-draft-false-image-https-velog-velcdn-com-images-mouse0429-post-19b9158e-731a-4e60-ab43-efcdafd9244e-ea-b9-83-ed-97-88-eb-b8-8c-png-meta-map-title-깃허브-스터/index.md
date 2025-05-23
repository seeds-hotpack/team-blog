---
meta:
  title: 깃허브 스터디
  description: 깃허브란 무엇인가
  date: 2025-05-23T16:22:00.000Z
author_info:
  author: 백하현
taxonomy:
  tags:
    - 태그github
  categories:
    - github
options:
  draft: false
  image: https://velog.velcdn.com/images/mouse0429/post/19b9158e-731a-4e60-ab43-efcdafd9244e/%EA%B9%83%ED%97%88%EB%B8%8C.png
date: 2025-05-23T16:33:42+09:00
---
깃허브란 무엇인가.  

1. 팁 협업 프로젝트를 진행하는데 도움이 되는 웹페이지 툴이다.  

이때 내가 내 개인 컴퓨터 내에서 진행하던 코드를 깃헙 웹페이지와 내 컴퓨터를 연동시켜서 깃헙 내에 내코드를 저장하는것도 가능하게 함  

즉, 코딩뿐 아니라 여러 실무적인 문제도 해결해줌 (내 코드를 하나하나 압축해서 공유할 필요가 없음) 코드를 저장 및 공유 그리고 협업을 하게하는 하나의 엡페이지 툴이라고 생각하면 됨  



깃허브 초기 세팅  

1. 깃허브에 로그인을 한다  

2. 로그인을 진행한 후에 Top repositories 란에 new를 클릭하면 새로운 프로젝트를 생성 할 수 있다 이곳에 내 코드를 올리거나 저장 및 협업가능한 스페이스가 되는 것 이떄 해당 레퍼지토리를 public,private 설정 가능  

3. 이때 프로젝트를 만들고 나면 git이라는 일종의 명령어 툴을 이용해서 소스 코드를 올리게 됨  

\-> github는 소스 코드를 올리는 공간, git -> 소스코드를 올려주는 툴 (를 사용하기 위해선 git을 다운받아야함)  

4. 이때 git을 사용하기 위해선 git bash에서 일종의 세팅 환경설정을 해줘야함  

\    1. git config --global user.name "사용할 이름" 입력  

\    2. git config --global user.email "내 깃허브 이메일" 입력  

\    3. 마지막으로 입력한 정보가 잘 들어 갔는지 확인하고 싶다면  

\    git config --list 를 입력해서 뜬 결과에서 내 유저네임과 이메일만 잘 기입이 되었는지 확인하면 됨 그러면 환결설정이 종료된거임  

5. 이제 실제로 내가 만든 프로젝트에 내가 만든 소스코드를 올리기 위해선 이제 코드를 작성한 응용프로그램에서 터미널을 들어간 후  

\    5-1. git init 을 입력 ("맨 처음"에 프로젝트의 틀을 올릴때만)  

\    5-2. git add . 을 이어서 입력 이때 .은(전부라는 의미) git add 자체는 어떤 파일을 올릴지 추가하는 과정 즉.은 자기가 작업한 모든 프로젝트의 파일을 올리겠다는 뜻임  

\    물론 파일 하나만 선택해서 올리는 것도 가능 git add index.html처럼  

\    5-3. git status add를 통해 내가 어떤 파일을 올렸는지 확인하는 거임 (필수 x)  

\    5-4. git commit -m "first commit" 이란 히스토리를 만드는것  

\    파일의 히스토리를 만드는것인데 이때 "first commit"애가 그 히스토리의 이름이 되는 것  

\    5-5. 히스토리를 만들어도 이 히스토리를 깃허브로 보내기 위한 내 로컬 프로젝트와 깃허브간의 연결을 구축해야함  

\    5-6. git remote add origin https://github.com/qorgkgus123/first.git  

\    이것이 그 연결고리를 구축해 줄 것 내가 처음에 만든 프로젝트로 내 소스 코드를 보낸다는 뜻임  

\    git remote -v 했을때 깃허브에 만든 프로젝트 이름이 뜨면 연결 성공인거임  

\    5-7. 마지막으로 git push origin master 를 입력하면  

\    (뒤에서 설명하지만 master브랜치라는 곳에 내 코드를 보내는 것인데  

\    이 master branch란 우리가 만든 총 프로젝트 소스코드의 배포판임)  

\    5-8. 이 모든 과정을 거치면 우리가 처음 프로젝트를 시작할 그리고 코드를 앞으로 새로 생성해서 검수받고 추가할  

\    최종 배포판(master, 프로젝트의 시점으로 보면 개인의 시점으로 보면 내가 만든 내코드를 저장 성공 한것임)  

\    일종의 틀이 만들어 진것임  



이제 새로운 코드가 추가가 되었을 때  

이미 프로젝트에 올린 코드를 추가하고 싶을 때  

1. 수정된 소스코드의 채널에서 git add . 입력 (하고 git status를 입력하면 수정된 파일이 뜸)  

2. 이렇게 수정되었으니 git commit -m "second commit"을 입력해서 수정된 파일의 히스토리를 만들어줌  

3. git push origin master 를 마지막으로 입력하면  

   이제 마스터 브랜치의 프로젝트에 내가 수정한 코드가 적용된 것을 확인 할 수 있다.  



++ 깃충돌  

서로 협업하는 과정에서 마스터 브랜치와 동일한 내용이 아닌 코드를 커밋하려는 과정에서 생기는 문제  

이러한 문제는 머지를 통해서 기존의 코드를 유지할지 새로운 코드를 받아들일지 결정한 해야함  

그리고 이렇게 결정을 하고 난 후에는 항상 마스터 브랜치에 그 코드를 갓허브 페이지에 git push origin master를 통해서 적용한 후  

하위 브랜치에서는 git pull origin master 해서 코드를 가져온 후 그 코드를 기반으로  

즉, 가장 최신의 업데이트 버전 코드로 항상 새로운 코드를 입력해야한다.  

하지만 충돌 자체를 막기에는 사실 어려운 부분이 너무 많기 때문에  

깃 충돌 예방이 더 중요  



1. 서로 다른 파일에서 작업을 하는것이 좋음  

2. 커밋을 자주자주해서 그때 그때 에러를 발견하는 것이 좋음  

3. 머지도 자주자주하는게 좋음  

4. pull도 자주  



그리고 이래서 마스터 브랜치와는 별개인 develop branch(마스터 브랜치의 복사본)를 만들어야 한다.  



git checkout -b develop  

git push 를 하면  

그다음 깃허브에 해당 브랜치도 깃허브 페이지에 올리기위한 명령어가 뜸  

그걸 입력하면 develop 브랜치도 같이 올라가게 됨  

이렇게 브랜치 세팅을 하고 마스터 브랜치는 read only와 pull request옵션을 주면  

우리는 우리가 만든 develop branch에서 작업한 내용만 가지고 마스터 브랜치에 접근 가능하다.  



git checkout develop  



한 후 깃허브의 프로젝트 페이지로 가면 보드형태로  

내가할 뭐 일 to do, in progress, done 형태로 진행할(이것이 issue정리) branch 새로운 브랜치를 생성하게 됨  

즉, feature A(내가 개인으로 구현할 코드 브랜치) -> develop (그 코드들이 임시로 모두 모이는 브랜치) -> master( develop 단계에서 모두 완성된 최종판) 이렇게 진행이 되는거고  

이때 feature A를 to do, in progress, done 이 단계를 거쳐서 만들어 가는 것  

이때 아까 말한 프로젝트 칸에서 해당 feature branch를 만들 수 잇는데  

이때 만들 때 또 어떤 코드의 복사본을 가져올지 master와 develop중 선택해야함  

이때는 꼭 develop branch의 복사본을 받아와야 함  



여기서 이렇게 프로젝트에서 각자 할일을 feature 형태로 나눠 놓았을 때  

어떠 임무 feature A를 배정받고 그쪽 브랜치로 넘어가서 작업을 진행하고  

해당 임무를 완성하면 해당 코드를 개발중인 develop branch로 push해야하는데  

이때 하는것이 PR(pull request)이다.  

이건 명령어가 아닌 깃허브 홈페이지에서 pull request 란에서 feature A -> develop 이렇게 리퀘스트를 할 장소를 선택하는란이 있고  

이것이 완성이 되면 거기에 어떤 코드를 작성했는지 간단한 리뷰글과 함게 PR진행 올리면  

팀장 or 팀원들이 코드 리뷰를 진행 후  

문제가 없으면(approve) (문제있으면 request changes)  

하위 브랜치코드들을 develop branch로 올림  

(위의 git push 기능을 사용해서 마구잡이로 합치지않는 듯 여기서도 PR진행 후 코드를 올림)  

최종적으로 merge pull request를 하면 완료  



이렇게 여러 feature들을 develop에 merge하다보면 conflict가 발생하는데  

(팀원마다 PR진행시 본인이 merge를 진행할 develop branch가 본인이 알던 develop 버전이랑 다를 수 있기 때문  

따라서 항상 PR실행하기전에  



git checkout develop  

git pull origin develop (모든 업데이트가 진행된 develop버전으로 업데이트)  

git checkout - (이건 넘어오기전 브랜치로 넘어가는 명령어(여기선 feature A))  



한 후 여기서  



git merge develop  



을 진행해서 먼저 내가 진행하는 브랜치에서 충돌을 잡아내거나 에러를 잡아내야함  

(충돌이 발생하면  

1. 기존의 코드를 유지  

2. 새로운 코드를 업데이트  

3. 두개의 코드를 전부 유지  

중 하나를 고르면 됨)  



그래서 각자가 맡은 feature를 모두 수행했으면 아까 말한 done 상태로 넘겨서 issue들을 처리하면됨  

완벽하게 처리되면 develop를 master로 merge를 진행
