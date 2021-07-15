## AWS CodeDeploy

클라우드 서비스를 운영하는 업무를 맡아서 진행하면서 10대에 WAS서버에 하나하나 들어가 배포를 진행하는 부분에 대해서 개선이 필요하다고 생각이 들어 AWS에 CodeDeploy를 활용해 가용영역별 트래픅 차단과 안정적인 소스배포를 진행하는 방향이 더욱 좋은 서비스를 만드는데 도움이 된다고 생각해서 적용을 진행했다.

### 앙꼬 없는 찐빵

사실 본인이 다니는 회사에서는 CI툴을 사용하지 않는다. 그래서 처음에는 CI/CD 구성하려고 했었다. 내부적인 사정과 2년정도 개발을 접한 상태로 이 두가지를 진행하며 업무를 처리하기에는 부담이 있었고 좀더 효율적인 업무 처리를 위해서 CD를 먼저 도입하기로 했다. 사실 남들이 보면 굉장히 우스운 상황일 수 있으나 개발자는 단순반복 작업을 줄이는 일을 하는 사람!!

WAS 10대에 각각 들어가 배포할 소스를 풀고 서버를 내렸다 올리는 작업을 반복하는 시간을 줄인다면 그 시간을 더욱 효율적으로 활용할 수 있다고 생각했다 그래서 나는 앙꼬 없는 찐빵과 같은 CI없는 CD를 진행했다.

### 자동배포를 하며 고려한 부분

1. 서비스를 이용하는 사용자들에게 영향이 없어야한다.
2. 소스 원복을 고려해야한다
3. Git Commit로그를 활용해서 배포해야할 소스를 만들어야한다.

### CodeDeploy

- appspec.yml  
  CodeDeploy에서 appspec.yml 파일은 어떤 방식으로 배포를 진행할지 정의하는 파일이다.  
  해당파일 안에서 서버가 종료되는 시점, 서버가 실행되는 시점 등등 다양한 경우에 어떤 쉘 파일을 실행할지 정하게 된다.

  ```
  version: 0.0
  os: linux
  files:
    - source: Config/config.txt
      destination: /webapps/Config
    - source: source
      destination: /webapps/myApp
  hooks:
    BeforeInstall:
      - location: Scripts/UnzipResourceBundle.sh
      - location: Scripts/UnzipDataBundle.sh
    AfterInstall:
      - location: Scripts/RunResourceTests.sh
        timeout: 180
    ApplicationStart:
      - location: Scripts/RunFunctionalTests.sh
        timeout: 3600
    ValidateService:
      - location: Scripts/MonitorService.sh
        timeout: 3600
        runas: codedeployuser
  ```

- 배포 환경 구성  
  각각 다른 가용영역에 위치한 WAS들을 태그로 묶는다.  
  1영역(WAS1,WAS2,WAS3) 2영역(WAS4,WAS5,WAS6) 3영역(WAS7,WAS8,WAS9,WAS10)  
  위치 한다면 WAS1,WAS4,WAS7 식으로 EC2 태그를 통해서 배포그룹을 생성했다.

  ![image](https://user-images.githubusercontent.com/38723165/125793515-804d8af0-2f56-4d38-a7c8-463239f5a47a.png)

- 배포구성  
  AWS에 미리 정의 된 배포 구성에는 CodeDeployDefault.AllAtOnce, CodeDeployDefault.HalfAtATime, CodeDeployDefault.OneAtATime로 정의되어 있다.

  CodeDeployDefault.OneAtATime의 경우 한번에 모든 인스턴스를 배포하고 모두 배포가 성공하면 배포 성공으로 표시돤다

  CodeDeployDefault.HalfAtATime의 경우 절반의 인스턴스를 한번에 배포하고 절반이 배포에 성공하면 배포 성공으로 표시돤다

  CodeDeployDefault.OneAtATime의 경우는 인스턴스를 하나씩 배포를 하고 하나라도 실패하면 배포 실패로 표시된다

  CodeDeployDefault.OneAtATime 안전하게 하나씩 인스턴스 배포를 진행하는 방식으로 배포구성을 잡았다. 직접 구성을 설정하는 부분도 있어서 추후에는 비율을 조금씩 늘려나갈 생각이다.

  ![image](https://user-images.githubusercontent.com/38723165/125793406-1f6260e2-418d-4d49-b969-b05b8fc690ab.png)

- 로드 밸런싱  
  CodeDeploy는 배포 할 인스턴스들을 대상그룹으로 만들어서 활성화 처리 하면 배포 중인 인스턴스에 대한 트래픽을 차단하고 배포가 완료되면 다시 트래픽을 허용하는 방식으로 배포를 진행해준다.

  ![image](https://user-images.githubusercontent.com/38723165/125794647-18b499eb-7989-4afe-925b-2a269d57b553.png)

# 배포 결과

작성중 ...
