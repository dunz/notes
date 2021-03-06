# Index

index : 디비의 검색을 빠르게 하기 위해 미리 데이터의 순서를 정리해두는 과정

인덱스 사용 시 주의할 점
- 인덱스를 사용하면 쿼리 수행시간이 단축됨
- 모든 쓰기 작업은 인덱스 때문에 더 오래 걸림
- 데이터가 변경될 때마다 문서 자체는 물론이고 몽고디비의 모든 인덱스를 갱신함
- 몽고디비는 콜렉션당 최대 64개까지의 인덱스를 가지도록 제한됨,
- 일반적으로 콜렉션에 2~3개 이상의 인덱스를 가지지 않는 것이 좋음
- 몽고디비의 인덱스는 전형적인 관계형 데이터베이스의 인덱스와 거의 흡사하게 작동됨
- 새로운 인덱스를 구축하면 오랜 시간이 걸리고 자원을 많이 잡아먹음
- 몽고디비는 인덱스 구축이 완료될 때가지 데이터베이스의 모든 읽기와 쓰기를 중단하면서 인덱스 구축을 하게 됨
- background 옵션을 사용하여 인덱스를 구축하면 다른 작업이 가능하기는 하지만 효율성이 떨어지고 포그라운드 인덱싱보다 훨씬 느림

# 샤딩의 목적
> 데이터의 분산 저장

단 한대의 서버에 빅데이터를 저장하는 것은 불가능 하다.
- 서비스의 성능 저하를 유발 : 초당 발생하는 엄청난 양의 Insert 동직시 Wirte scaling 문제 발생
- 디스크를 사용하는 하드웨어 한계성
- 몽고디비는 데이터를 분산하여 순차적으로 저장하기 때문에 여러개의 서버가 트래픽을 분산해서 처리해주는 효과를 가질 수 있다.

> 백업과 복구 전략

데이터를 분산한다는 샤딩의 가장 대표적인 기능을 통해 얻는 효과
- 시스템의 성능 향상
- 데이터 유실 가능성으로부터 보호

> 빠른 성능

여러 대의 독립된 프로세스가 병렬로 작업을 동시에 수행하기 때문에 이상적으로 빠른 처리 성능을 보장받는다.

# 샤드 시스템 구성 팁
- 최소 2대만 있으면 샤드 서버 구축이 가능하지만 가능한 성능 보장을 위해 3대 이상의 서버를 샤드로 활용하는 것을 추천
- 샤드 시스템 구축시 사용하는 라우팅 서버인 mongos와 OpLog, Balancer프로세스가 추가로 메모리를 사용하기 때문에 기존에
싱글 서버보다 20~30% 정도 추가로 메모리 준비 필요

# 샤드 config 서버 개요
> 샤드 시스템 구축시필요한 Config 서버의 특징

- Config 서버는 샤드 시스템에 대한 메타데이터 저장/관리 역할
- 샤드 서버의 인덱스 정보를 빠르게 검색 가능케 함
- 샤드 서버와 별도의 서버 구축이 기본
- 장애 발생에 대비하여 최소 3대 이상 사용(최소 1대만으로도 운영가능)
- 샤드 서버에 비해 저사양 서버 사용 가능

# mongos 서버 특징
> mongos 애플리케이션의 주요 특징

- 하나 이상의 프로세스를 사용한다.
- Config 서버의 Meta-data를 캐시한다.
빅데이터를 샤드 서버로 분산해주는 프로세스이다.

# 샤드키 구성
> 샤드키 구성방법이 몽고디비 샤딩 시스템을 구축할 때 가장 중요하다.
샤드키는 여러개의 샤드서버로 분할될 기준 필드를 가리키며, 앞으로
partition과 load balancing에 기준이 된다. 때문에 mongodb 데이터 저장과 성능에 절대적 영향을 미친다.

# 복제의 개념과 동작 원리
복제(Replication)
- 고성능 DB에서 가장 핵심이 되는 기능
- 같은 데이터를 갖는 여러개의 MOngoDB서버를 설계하는 과정
- 고성능의 미러링 기능
- 성능과 높은 가용성의 장점 제공
- Master/Slave Replication
  - 자동 장애 조치 불가능 (수동)
  - 13개 이상 노드까지 구성 가능
  
복제의 용도
- 데이터의 일관성
- 읽기 분산
- 운영 중 백업
- 오프라인 일괄 작업용 데이터 소스

복제 동작 원리
- 몽고디비의 마스터는 쓰기 연산을 담당
- 일반 마스터-슬레이브 방식과 동일하게 쓰기는 마스터에서만 이루어짐
- 몽고디비의 쓰기 연산이 실행되면 데이터 저장소와 Oplog 영역에 명령어 자체도 저장
- B+ 트리로 구성된 데이터 저장소는 쓰기 연산을 수행한 결과만을 저장
- Oplog 에는 연산 수행과 관련된 명령 자체를 타임 스탬프와 함께 저장
- 몽고디비는 슬레이브가 주기적으로 마스터에게 자신의 optime보다 더 큰 oplog를 요청
- 5초안에 마스터에서 쓰기 연산이 발생하면 바로 데이터를 응답
- 5초안에 발생하지 않는다면 데이터가 존재하지 않는다는 응답을 보내줌
- 슬레이브는 요구한 Oplog 데이터가 존재하면 자신의 Oplog에 데이터를 저장한 다음 바로 마스터에 다시 Oplog를 요청

# 리플리카셋
- primary 서버 : 리플리카셋에서 첫번째 입력을 담당하는 서버
- secondary 서버 : primary 서버를 제외한 나머지 서버
- 만약 primary 서버에 문제가 생기면 자동으로 secondary 서버에서 데이터 입/출력을 담당
- primary 서버는 secondary 서버를 2초 단위로 상태 체크하여 동기화를 위해 HeartBeat 확인
- HeartBeat 수신 결과 세컨더리 서버를 사용할 수 없는 상황이 되더라도 데이터 복제만 중단될 뿐 프라이머리 서버는 데이터 수신/저장을 계속 담당
- secondary 서버가 복구되면 그간 밀린 데이터를 복구해주기 위해 Primary 서버는 OpLog를 저장하게 되는데 이후 secondary가 복구되면 자동으로 동기화 해줌
줌 
- 만일 primary 서버가 장애 상황이 된다면 secondary 서버를 primary 서버로 만듬

# 리플리카셋 한계
- 한 복제 집합을 구성할 수 있는 노드의 최대 개수는 12개 => 50개
- 한 복제 집합에서 투표를 할 수 있는 노드의 최대 개수는 7개
- 슬레이브가 아무리 빨리 데이터를 동기화한다고 해도 마스터와의 통신지연 시간만큼의 차이를 가질 수 있음
- 부하를 견디지 못해서 마스터 서버가 죽었을 경우 Oplog에 동기화 되지 않은채 남아있는 데이터 연산을 잃어버리는 현상이 발생할 수 있음
- 저널링 데이터를 저장하는 방법의 경우에도 group commits 주기(100ms) 안에 데이터가 존재하는데도 시스템이 다운되어 메모리에 저장된 데이터가 날라갔을 경우에는 복구가 불가능함- 



 
  
  
  
  
  
  
  


