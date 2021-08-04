# URL ( Uniform Resource Identifier ) 설계 - 2

### 문서 (Document)

- 단일 개념 (파일하나, 객체 인스턴스, 데이터베이스 row)
- /member/1

### 컬렉션 (Collection)

- 서버가 관리하는 리소스 디렉터리
- 서버가 리소스의 URI를 생서앟고 관리
- /members

### 스토어 (Stroe)

- 클라이언트가 관리하는 자원 저장소
- 클라이언트가 리소스의 URI를 알고 관리

### 컨트롤러, 컨트롤러URI (Controller)

- 문서, 컬렉션, 스토어로 해결하기 어려운 추가 프로세스 생성
- 동사를 URI에 직접 사용
- /members/{id}/edit
