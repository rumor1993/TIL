# SlowQuery를 찾는방법

서비스를 운영하면서 슬로우 쿼리로 인해 장애가 발생하는 경우를 많이 보았다. 서버에 영향을 주는 SlowQuery를 AWS RDS에 설정을 통해서 잡아내는 설정을 이번에 서비스에 적용했다.

### 로그의 필요성

현재 플로우에 서비스는 스카우터 모니터링을 통해서 슬로우 쿼리나 서버에 이상을 처리하고 있다. 그럼에도 RDS에 로그를 남기는 이유는 스카우터에 경우 모니터링을 계속 하고 있는게 아니라면 슬로우 쿼리가 어떤 부분인지 찾기가 어렵고 또한 파라미터에 대한 값을 정확히 대입해서 확인해보기가 어렵다. 물론 스카우터에 알럿이나 다양한 설정을 통해서 가능할 수 있겠지만 아직은 많이 부족하다보니 좀더 빠르고 간단하게 슬로우 쿼리를 찾을 수 있는 RDS에 파라미터 설정을 통한 로그를 남기기로 했다.

### 주의점

로그는 결국 텍스트파일을 서버에 남기게 되는 부분으로 보통 로그로테이트를 통해서 로그를 관리해준다. WAS서버의 경우도 로그를 DEBUG모드로 해놓고 돌리지 않는 이유는 많은 로그가 쌓이면 성능에 이슈도 있지만 가장 큰 이유는 서버에 용량을 모두 사용하면 서버가 뻗어버린다는 점이다 RDS에 로그를 설정하는 부분 역시 서버에 볼륨을 사용하는 작업이므로 RDS에 볼륨에 임계치를 설정하고 알럿이 오면 이 로그 파일을 관리 해주는 설정을 필수로 해주어야 한다.

### 설정방법
1. RDS 파라미터 그룹
 - 파라미터그룹에 파리미터 설정을 수정한다.

2. log_statement
ddl은 모든 DDL(데이터 정의 언어) 문(예: CREATE, ALTER 및 DROP)을 로깅합니다.
mod는 모든 DDL 및 DML(데이터 수정 언어) 문(예: INSERT, UPDATE 및 DELETE)을 로깅합니다.
all은 실행 시간에 상관없이 모든 쿼리를 로깅합니다.



# 배포 결과

작성중 ...