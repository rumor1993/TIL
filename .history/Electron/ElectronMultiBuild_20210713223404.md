## Electron Build

기존에 사용하던 Mac을 사용하지 못하는 상황이 생겨서 일렉트론 빌드를 못하는 상황이 생겼다.  
문제는 Mac Catalina 이후버전의 경우 32비트 앱에 대한 지원을 종료했기에 32비트를 지원하는 프로그램을 생성할 수 없다.  
따라서 Window에서 빌드를 진행 해야하지만 집에 Windows 운영체를 가진 PC가 없어서 다른 방법을 찾기로 했다.

### Multi Platform Build

지금 당장 간단하게 빌드를 진행하기 위해서 AWS 리눅스용 EC2를 생성하고 Electron 소스를 Git으로 내려받았다.  
해당 EC2에서 빌드를 진행했으나 리눅스에서도 Windows 파일을 빌드를 지원하지 않았다.  
[Electron-Bulider 공식사이트](https://www.electron.build/multi-platform-build#to-build-app-in-32-bit-from-a-machine-with-64-bit)를 통해서 리눅스에서 Windows 파일을 빌드하는 방법을 찾아서 적용하기로 했다.

사이트에서는 도커를 이용해서 컨테이너를 띄우고 그 안에서 빌드를 진행하는 방식을 권장했다.

1. Run docker container:

   ```
    docker run --rm -ti \
   --env-file <(env | grep -iE 'DEBUG|NODE_|ELECTRON_|YARN_|NPM_|CI|CIRCLE|TRAVIS_TAG|TRAVIS|TRAVIS_REPO_|TRAVIS_BUILD_|TRAVIS_BRANCH|TRAVIS_PULL_REQUEST_|APPVEYOR_|CSC_|GH_|GITHUB_|BT_|AWS_|STRIP|BUILD_') \
   --env ELECTRON_CACHE="/root/.cache/electron" \
   --env ELECTRON_BUILDER_CACHE="/root/.cache/electron-builder" \
   -v ${PWD}:/project \
   -v ${PWD##*/}-node-modules:/project/node_modules \
   -v ~/.cache/electron:/root/.cache/electron \
   -v ~/.cache/electron-builder:/root/.cache/electron-builder \
   electronuserland/builder:wine

   ```

2. Type in yarn && yarn dist  
   본인의 경우 따로 dist 명령어를 통해서 빌드를 진행하는 방식을 사용하고 있지 않아서 빌드를 진행하는  
   sh파일을 실행시키는 방식으로 진행했다.
   빌드폴더를 확인해보니 windows (32/64)를 지원하는 exe 파일과 dmg파일 모두 생성된것을 확인했다.

### 문제점

1.  Code Signing  
    Mac의 경우 Code Signing을 진행하려면 맥OS에서만 처리가 가능하다. 해당부분에 대해서는 다른 방안이 필요하다.  
    우선은 사용중인 Mac을 이용해서 빌드를 진행하는 방식으로 진행 예정
2.  Windows 파일 테스트  
    빌드된 exe파일을 실행하기 위해서는 Windows PC가 필요한데 이를 확인하기가 불가능한 상태이다.  
    다른 사람의 도움을 받아서 진행하려고 했으니 테스트나 직접적인 상황을 보기가 힘들다고 판단했고  
    AWS Windows EC2를 생성해서 테스트를 진행했다.

### EC2 Windows 인스턴스

윈도우 인스턴스를 생성하고 IE에서 파일을 다운로드 받으려고 했으나 정책으로 인해 막혀있어 이를 제거해주는 작업이 필요하다.

1. Server Manager 설정
   <img src="https://user-images.githubusercontent.com/38723165/125459607-76a1ede1-d927-4584-87e4-19c8b866b6da.png">
2. Local Server 설정
   <img src="https://user-images.githubusercontent.com/38723165/125459807-47773317-c67f-44fd-91e6-a2a9545b9b0a.png">
3. IE Enhanced Security Configuration 설정
   ![image](https://user-images.githubusercontent.com/38723165/125460124-0b67e287-2331-4060-a20a-4c77b1b19f1a.png)
   IE Enhanced Security Configuration 설정에 ON을 클릭하면 팝업이 뜨고 해당 팝업에서  
   사용자/관리자 모두 OFF 변경 후 크롬을 다운받은 후 다시 ON으로 설정해주면 된다.
