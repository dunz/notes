### MongoDB
> 10gen사에서 C++로 개발한 Scheme-less NOSQL 데이터베이스. 데이터를 JSON형태로 관리하며 빅데이터 처리 속도 향상을 위해 JOIN지원하지 않음


### MongoDB 장점/단점
#### 장점
- 대용량의 데이터를 신속하게 처리가능
- 시스템 확장 및 장애 대응 가능
- 스키마 변경에 따른 리스크가 적음

#### 단점
- 분산시스템이기 때문에 데이터 유실 가능성이 있음

### MongoDB 주요기능
- 전용 Query를 이용한 데이터 입출력
- 인덱싱 기능
- 데이터 복제 및 장애 대응
- 데이터 분산저장(Sharding)
- 데이터 분산 연산(MapReduce) 기능

### 데이터모델 설계 - Data Model Design

#### Embedded Data Models
> - 관계형DB와는 다르게 조인을 사용하지 않기 때문에 빠른 성능의 쓰기와 읽기가 가능한데 이것을 가능하게 해주는 설계 구조를 Rich Document라고 한다.
- MongoDB는 중첩데이터(Embeded Document) 구조를 설계할 수 있기 때문에 불필요한 조인을 최소화할수있는 데이터구조를 설계할 수 있다.

- 쿼리가 단순해지고 조인할 필요가 없기 때문에 도큐먼트단위의 데이터 저장에 효과적이며 빠른 성능이 보장된다.
- 데이터 보안에 효과적이다.
- 하지만 Embedded되는 도큐먼트의 크기는 최대 16MB 범위에서 가능하다.

#### Normalized Data Models
- 정규화 = 데이터 중복 제거, 무결성보장
> MongoDB에서는 Link, $lookup연산자 등을 이용해 분리저장하고 분리된 각각의 컬렉션에서 조회할 수 있다.

Link :

- ObjectId 이용

```sh
db.order.insert({customer_name:'이동주', total:50000, pay_type:'credit'})

o = db.order.findOne()
{
	"_id" : ObjectId("59118b8deb2c905e6931c6ca"),
	"customer_name" : "이동주",
	"total" : 50000,
	"pay_type" : "credit"
}

db.order_detail.insert({ord_id:o._id, items:[{p_name:'과자', price:30000}, {p_name:'신발', price:20000}]})

db.order_detail.find({ord_id:o._id}).pretty()
{
	"_id" : ObjectId("59118c2aeb2c905e6931c6cb"),
	"ord_id" : ObjectId("59118b8deb2c905e6931c6ca"),
	"items" : [
		{
			"p_name" : "과자",
			"price" : 30000
		},
		{
			"p_name" : "신발",
			"price" : 20000
		}
	]
}
```

- DBREF 함수 이용

```sh
db.order_detail.insert({ord_id:[new DBRef('order', o._id)], items:[{p_name:'과자', price:30000}, {p_name:'신발', price:20000}]})

db.order_detail.findOne()
{
	"_id" : ObjectId("5911935feb2c905e6931c6cf"),
	"ord_id" : [
		DBRef("order", ObjectId("591192caeb2c905e6931c6ce"))
	],
	"items" : [
		{
			"p_name" : "과자",
			"price" : 30000
		},
		{
			"p_name" : "신발",
			"price" : 20000
		}
	]
}
```


#### Denormalized Data Models
- 비정규화 = 데이터 중복 허용
- 쿼리프로세싱을 간단하게 하거나 최적화하기위해 중복을 허용함

비정규호로 인한 트레이드 오프
- 쿼리당I/O횟수 또는 쿼리 사이즈 및 복잡도 vs 전체 데이터 사이즈

> 수평적 확장이 어려운 RDBMS와 확장성이 좋기 때문에 데이터 사이즈 부분에 있어서는 더 관대하다. 


### 운영요소 및 데이터모델 - Operational Factors and Data Models

#### Document Growth
- 업데이트를 하게 될경우 필드가 추가되거나 배열 요소에 값이 push되는 상황에서 document사이즈가 커질 수 있다.
- MMAPv1엔진의 경우 해당 document에 할당된 공간을 초과하면 document를 재배치 하지만 Power of 2 Sized Allocations을 사용하게되면 레코드공간을 효과적으로 재사용 할 수 있을 뿐만 아니라 재배치 발생을 최소화한다.
- 재할당을 명시적으로 피하기 위해 사전 할당 전략을 사용할 수도 있다.

#### Atomicity
- 몽고디비의 모든 쿼리는 원자적이다
- 한번에 하나의 document 또는 collection에서만 쓰기/갱신 작업이 가능하다

#### Sharding
- 몽고디비는 샤딩으로 수평확장을 지원한다.

#### Indexes
- 인덱스를 사용하여 일반 쿼리의 성능을 향상시킨다
- 인덱스를 작성하면 약간의 쓰기 성능 저하가 있다.
- 쓰기보다 읽기작업이 많을 경우 인덱스가 효과적이다.

#### Large Number of Collections
- 로그문서를 저장하는 로그 수집 collection을 고려 할 수 있다.
- 로그의 유형별로 collection을 그룹화 할 수 있다. (logs_dev, logs_debug)
- 각 collection에는 몇 kb의 최소 오버헤드가 있다.
- _id색인을 포함하여 각 인덱스는 최소 8kb의 데이터공간이 필요하다.

#### Storage Optimization for Small Documents
> document에는 일정량의 오버헤드가 있는데 일반적으로는 중요하지 않지만 각 컬렉션의 document에 하나 혹은 두개의 field만 있는 경우에는 오버헤드가 클 수 있다
 이럴 경우 고려해야 할 사항이 있다.
 
- _id필드를 명시적으로 지정해라 (작은 document의 경우 기본 할당되는 ObjectId는 상당한 공간을 차지할 수 있다)
- 짧은 field명을 사용하라 (오버헤드보다 표현력을 중시할 경우 구지 표현력을 해쳐가며 field명을 사용할 필요는 없다)

#### Data Lifecycle Management¶
- 컬렉션의 TTL (Time to Live) 기능은 일정 기간이 지나면 문서를 만료시킨다
- 일정기간동안 데이터를 유지해야 하는 경우 TTL기능을 고려할 수 있다.
- 어플리케이션에서 최근 삽입된 document만 사용하는 경우 capped collection을 고려할 수 있다.
- 


# Replica Set
- 프라이머리 서버는 매2초마다 세컨더리 서버의 상태를 체크하며 동기화를 위한 HeartBeat 작업을 수행한다.
- 만약 세컨더리 서버가 중지 되더라도 복제 작업만 중지될 뿐 프라이머리 서버에 대한 데이터 읽기 작업은 정상적으로 수행된다.
- 만약 프라이머리서버가 중지되면 세컨더리 서버가 프라이머리 서버가 된다.
- OpLog는 프라이머리 서버가 세컨더리 서버로 복제 작업을 수행하다 장애로 인해 작업을 수행할 수 없는 경우 동기화하지 못한 데이터를 추후 세컨더리 서버로 반영해주기 위해 데이터를 백업해 준다.
- 프라이머리 서버에 장애가 발생하면 mongoDB는 10초 이내에 다음 Primary 서버가 되어야 할 노드 하나를 선택한다.
- 아비터서버가 활성화되어 있다면 아비터가 적절한 서버를 선택해 주고 사용자가 각 서버에 대한 우선순위를 설정해 둔 경우에는 가장 높은 값을 부여받은 서버가 프라이머리 서버가 된다. (Priority 범위: 0~1000)

### 멤버의 유형
- Secondary Only Member: 절대 프라이머리 서버는 될 수 없는 Only Secondary 서버를 의미한다.
    > `rs.reconfig({members:[{priority:0}]})`
- Hidden Member: Client Application을 위한 숨겨진 멤버, 투표 시 사용되지 않음.
    > `rs.reconfig(members:[priority:0, hidden:true])`
- Arbiter Member: 사용자 데이터가 저장되지 않으며 오직 장애발생시 Primary 서버를 선출하기 위한 투표시에만 사용됨.
    > `db.runCommand({"replSetInitiate:{members:[arbiterOnly:true]}}"})`
- Delayed Member: Primary서브의 OpLog정보를 정의된 시간 동안 Secondary 서버에 적용하지 않고 Delay한 후 적용되는 멤버.
    > `rs.reconfig({members:[{priority:0, slaveDelay:3600}]})`
- Non Voting Member: 사용자 데이터를 저장하고 있지만 투표 시에는 Primary 서버로 선택되지 않는 멤버.
    > `rs.conf({members:[{votes:0}]})`

# Replica Set Deployment Architectures

설계 전략
- Maximum Number of Voting Members
  > 리플리카셋에는 최대 50개의 서버까지 가능하지만 투표권이 있는 서버는 최대 7개만 가질 수 있다. 추가 서버는 투표권이 없는 서버여야 한다.

- Deploy an Odd Number of Members
  > 서버가 짝수개라면 홀수개가 될 수 있도록 아비터를 배치해라

- Consider Fault Tolerance
  > 새로은 primary 서버를 선출하기위해 투표권이 있는 서버를 구성된 서버수의 과반수를 유지해라

- Use Hidden and Delayed Members for Dedicated Functions
  > hidden, delay 멤버를 사용하여 일정주기 백업 혹은 보고시 사용 가능 한다.

- Load Balance on Read-Heavy Deployments
  > 읽기 트래픽이 너무 많은 경우 secondary 서버에게 읽기를 할당하여 읽기 처리량을 향상시킬 수 있다.

- Add Capacity Ahead of Demand
  > 현재 셋이 용량을 초과하기 전에 항상 새 서버를 추가해라.

- Distribute Members Geographically
  > 장애발생시 데이터를 보호하려면 적어도 하나의 서버는 다른 데이터 센터에 두어라.

- Use Journaling to Protect Against Power Failures
  > 기본적으로 저널링이 사용되는데 예기치않은 장애 발생시 데이터 손실을 줄일수 있다.

# Three Member Replica Sets
> 리플리카셋은 최소 세개의 서버 혹은 두개의 서버와 아비터 서버로 이루어질수 있다.

[Manual](https://docs.mongodb.com/manual/core/replica-set-architecture-three-members/)

# Replica Sets Distributed Across Two or More Data Centers
> 리플리카셋은 단일 인스턴스 장애에 대한 기본적인 보호 기능을 제공하지만
서버가 모두 단일 데이터 센터에 있고 그 데이터센터에 문제가 발생했을경우  리플리카셋 전체가 문제가 생기게 된다.
그리하여 지리적으로 구별되는 데이터 센터에 복제 세트 서버를 배치하여 내결함성을 높일수있다.

- 데이터 센터에 장애가 발생할 경우 데이터를 보호하려면 적어도 하나의 서버는 대체 데이터 센터에 두어야 한다.
- 가능한 경우 홀수개의 데이터 센터를 사용한다.

예를 들어

- Three member replica set

```
2서버 + 아비터 일 경우 아비터는 하나의 서버와 같이 배치하여야 한다.
데이터센터1 : 아비터 + 데이터서버1
데이터센터2 : 데이터서버2
데이터센터1이 다운될 경우 리플리카셋은 읽기전용 상태가 되고 
데이터센터2가 다운될 경우 리플리카셋은 선거를 진행할 수 있으므로 새로운 primary 선출을 하여 쓰기 가능 상태가 유지된다.
```

- Five member replica set

```
데이터센터1: 3명
데이터센터2: 2명
데이터센터1이 중단되면 리플리카셋이 읽기전용이 된다.
데이터센터2가 중단되면 데이터센터1의 구성원이 선거가 가능하므로 primary 를 선출하여 쓰기 가능 상태로 유지된다.

데이터센터1: 2명
데이터센터2: 2명
데이터센터3: 1명
하나의 데이터 센터가 중단되도 나머지 데이터센터의 구성원들이 선거가 가능해 쓰기 가능 상태로 유지된다.

네트워킹 제한 또는 제한된 리소스가 있는 구성원일 경우 primary 서버가 되지 않도록 priority를 0으로 구성한다.
```

# Replica Set Protocol Versions
> MongoDB는 복제본 프로토콜 버전 0 (pv0) 및 복제본 프로토콜 버전 1 (pv1)을 제공합니다.<br/>
pv1은 MongoDB 3.2 이상에서 생성 된 모든 새로운 복제 세트의 기본값입니다.

- pv0는 w:1 쓰기의 롤백 가능성을 낮추는 데 우선 순위를 둡니다. 
- 그러나 pv0은 특정 네트워크 파티션 시나리오에서 확인 된 w :majority 쓰기가 손실 될 수 있습니다.
- pv1은 확인 된 w:majority 쓰기의 보전을 보장합니다. 
- pv1은 기본적으로 w:1 쓰기 보존보다 빠른장애조치에 우선 순위를 두지만 반대로 느린장애조치로 희생하며  w:1 쓰기의 보존에 우선순위를 구성하도록 구성 할 수 있습니다.

##### pv0, pv1 차이점
- pv0은 모든 MongoDB 버전에서 사용할 수 있습니다.
- pv1은 MongoDB 버전 3.2 이상에서 사용 가능하며 버전 3.2 이상에서 생성된 모든 Replica Set에서 기본으로 지정되어있다.


##### Arbiters
- 중재자가있는 복제 세트의 경우 pv1은 pv0에 비해 w:1 쓰기의 롤백 가능성을 높입니다.

##### Vetoes
- pv0는 회원들이 회원의 optime 및 우선 순위 값에 따라 선거를 거부 할 수 있도록합니다.
- pv1은 거부권을 사용하지 않습니다. 개별 회원은 특정 선거에서 후보자를 찬성하거나 반대 할 수는 있지만 일방적으로 선거를 거부 할 수는 없습니다.

##### Detection of Simultaneous Primaries
- 어떤 상황에서는 Replica Set의 두 노드가 일시적으로 자신이 Primary노드라고 믿을 수 있지만 그중 하나는 w:majority 쓰기 작성을 완료 할 수 있습니다.
- w:majority 쓰기를 완료 할 수있는 노드는 현재 Primary노드이며, 다른 노드는 일반적으로 네트워크 파티션으로 인해 강등을0 인식하지 못한 이전 Secondary 노드입니다. 
- 이 경우 이전 Primary서버에 연결하는 클라이언트는 기본 읽기요청을 했음에도 불구하고 오래된 데이터를 볼 수 있으며 이전 Primary서버에 대한 새로운 쓰기는 결국 롤백됩니다.
- pv0는 시계동기화에 의존하여 동시 Primary가 발생될수 있는데 시계 동기화에 대한 의존는 w:majority 기록의 손실로 이어질 수 있습니다.
- 시계 동기화 대신 pv1은 terms 개념을 사용합니다. 이를 통해 짧은 시간 내에 Primary를 신속하게 탐지하고 다수의 성공적인 선거를 실시 할 수 있습니다. 
- pv0는 단시간 내에 다수의 선거가 필요하면 primary가 없는 복제본 세트를 남겨 둘 수 있습니다.

##### Back to Back Elections
- 쓰기 가용성을 최대화하기 위해 pv1은 선거를 진행할 때 우선 순위를 고려하지 않습니다.
- 대신 Replica Set이 안정된 Primary을 가진 후에 pv1은 가장 우선 순위가 높은 Secondary를 선거에 호출하도록 "최선의 노력"을 시도합니다.
- 우선 순위가 높은 자격있는 회원이 선거를 부를 수 있기 때문에 이것은 연속적인 선거로 이어질 수 있습니다.
- 그러나 연속된 선거 사이에 30초의 buffer를 포함해야하는 pv0와는 달리, pv1에서 terms를 사용하면 연속 된 선거가 더 빨리 수행 될 수 있습니다.
- 증가 된 빈도와 pv1을 가진 연속적인 선거 사이에 시간 buffer가 없기 때문에 w:1 쓰기의의 롤백의 가능성이 높아집니다.
- 그러나 catchUpTimeoutMillis 설정을 높이면 롤백 횟수를 줄일 수 있습니다.
- pv0을 사용하면 선거 중에 우선 순위 값을 기준으로 거부 할 수 있습니다.
- 따라서 복제 세트가 안정된 Primary을 가진 후에는 pv0가 pv1보다 연속적인 선거가 적습니다.
- pv0는 시계 동기화를 사용하여 여러 Primary노드를 감지하기 때문에 pv0에는 잘못된 시계 동기화 방지를위한 연속된 선거 사이에 30 초 버퍼가 포함됩니다.

##### Double Voting
- terms을 사용하여 pv1은 선거에서 한 회원의 이중 투표를 방지합니다.
- pv0는 30 초 버퍼를 통해 이중 투표 할 가능성을 줄이지 만 선거가 30 초를 초과하면 구성원이 이중 투표하지 않을 것이라고 보장 할 수 없습니다.


##### Modify Replica Set Protocol Version
- 복제본 집합 프로토콜 버전을 변경하려면 복제본 집합을 새 protocolVersion으로 다시 구성하십시오(rs.reconfig).
- 예를 들어 pv1로 업그레이드하려면 mongo 셸을 현재 Primary노드에 연결하고 다음과 같은 작업 순서를 수행합니다.

```sh
cfg = rs.conf();
cfg.protocolVersion=1;
rs.reconfig(cfg);
```
> w:1 롤백의 가능성을 줄이려면 복제 세트를 더 높은 settings.catchUpTimeoutMillis 설정으로 다시 구성 할 수도 있습니다.

# Master Slave Replication
> 3.2 버전부터는 사용되지 않습니다 MongoDB 3.2에서는 공유 된 클러스터의 구성 요소에 대해 Master-Slave Replication를 사용하지 않습니다.<br/>
Replica Set의 대부분 사용사례에서 Master-Slave Replication를 대체합니다.<br/>
가능한 경우 모든 새로운 프로덕션 배포에 대해 Master-Slave Replcation이 아닌 Replica Set을 사용하십시오.<br />
이 설명서는 레거시 배포 및 보관 목적으로 만 지원됩니다.

- Master-Slave 배포의 모든 기능을 제공하는 것 외에도 Replica Set은 프로덕션 용도로 더욱 강력합니다.
- Master-Slave Replication은 Replica Set을 선행했기 때문에 다수의 비 마스터 (즉, 슬레이브) 노드를 가질 수 있었고 복제 된 연산을 단일 데이터베이스에만 제한 할 수있었습니다.
- 그러나 Master-Slave Replication은 중복성을 줄이고 장애 조치를 자동화하지 않습니다.

##### Fundamental Operations
- Master-Slave 배포를 구성하려면 두 개의 mongodb 인스턴스를 시작합니다. 하나는 마스터 모드이고 다른 하나는 슬레이브 모드입니다.

마스터 모드에서 mongod 인스턴스를 시작하려면 다음과 같이 mongod를 호출하십시오.
```sh
mongod --master --dbpath /data/masterdb/
```

- --master 옵션을 사용하면 mongod가 slave로부터 작업을 복제하기 위해 적용 할 작업을 대기열에 넣는 "작업 로그"인 local.oplog. $ main 컬렉션을 생성합니다.
- --dbpath는 선택적입니다.

슬레이브 모드에서 mongod 인스턴스를 시작하려면 다음과 같이 mongod를 호출하십시오.
```sh
mongod --slave --source <masterhostname><:<port>> --dbpath /data/slavedb/
```

- --source 인수에 대한 마스터 인스턴스의 호스트 이름과 포트를 지정하십시오.
- --dbpath는 선택적입니다.
- 종속 인스턴스의 경우 MongoDB는 원본 서버에 대한 데이터를 local.sources 컬렉션에 저장합니다.

##### Configuration Options for Master-Slave Deployments
- --source 런타임 옵션을 지정하는 대신 mongo 쉘의 다음 작업에서와 같이 마스터 인스턴스를 지정하는 local.sources에 문서를 추가 할 수 있습니다

```sh
use local                                                                   // 컨텍스트를 로컬 데이터베이스로 전환합니다.
db.sources.find()                                                           // 소스 컬렉션에 문서가 없도록 find () 작업이 문서를 반환하지 않아야합니다.
db.sources.insert( { host: <masterhostname> <,only: <databasename>> } );    // 원본 문서를 local.sources 컬렉션에 삽입한다.
```

###### host
- 호스트 필드는 마스터 mongod 인스턴스를 지정하고 호스트 이름, 즉 IP 주소 또는 호스트 파일로부터의 이름, 또는 바람직하게는 완전한 도메인 명을 포함하는 확인 가능한 호스트 명을 보유한다.
- mongod가 기본 27017 포트에서 실행되지 않으면 <: port>를 호스트 이름에 추가 할 수 있습니다.

###### only
- 선택 과목. 데이터베이스의 이름을 지정하십시오. 지정되면 MongoDB는 지정된 데이터베이스 만 복제합니다.

##### Operational Considerations for Replication with Master Slave Deployments
- 마스터 인스턴스는 capped 컬렉션 인 oplog에 작업을 저장합니다.
- 결과적으로, 슬레이브가 마스터의 상태보다 너무 늦게 떨어지면, "캐치 업"할 수없고 처음부터 다시 동기화해야합니다.
- 다음과 같은 경우 슬레이브가 마스터와 동기화되지 않을 수 있습니다.

> 슬레이브는 해당 마스터에서 사용할 수있는 데이터 업데이트보다 훨씬 뒤쳐져 있습니다.<br/>
슬레이브는 정지하고 (즉, 셧다운) 마스터가 마스터로부터 관련 동작을 겹쳐 쓰고 나중에 재시작한다.

- 슬레이브가 동기화되지 않으면 복제가 중지됩니다. 관리자는 수동으로 개입하여 복제를 다시 시작해야합니다. resync 명령을 사용하십시오.
- 또는 --autoresync를 사용하면 슬레이브가 마스터와 동기화되지 않을 때 10 초 후에 자동으로 복제를 다시 시작할 수 있습니다.
- --autoresync를 지정하면 (자), 슬레이브는 10 분간에 1 회만 재차 동기화를 시도합니다.
- 이러한 상황을 방지하려면 mongod를 시작할 때 --oplogSize 옵션을 추가하여 마스터 인스턴스를 시작할 때 더 큰 oplog를 지정해야합니다.
- --oplogSize를 지정하지 않으면 mongod는 시작시 사용 가능한 디스크 공간의 5 %를 oplog에 할당합니다 (64 비트 시스템의 경우 최소 1GB, 32 비트 시스템의 경우 최소 50MB).

##### Run time Master-Slave Configuration
- MongoDB는 Master-Slave 배치에서 mongod 인스턴스에 대한 여러 명령 행 옵션을 제공합니다.

###### Diagnostics
마스터 인스턴스에서 mongo 쉘에서 다음 조작을 실행하여 마스터의 관점에서 복제 상태를 리턴하십시오.

```sh
rs.printReplicationInfo()
```

> 버전 2.6의 새로운 기능 : rs.printReplicationInfo (). 이전 버전에서는 db.printReplicationInfo ()를 사용

복제 작업의 상태를 반환하려면 serverStatus를 다음 작업과 같이 사용하십시오.

```sh
db.serverStatus( { repl: 1 } )
```

##### Security
- 권한이 활성화 된 상태로 실행될 때, Master-Slave 배치에서, 마스터 mongod 인스턴스가 마스터 mongod 인스턴스를 인증하고 통신 할 수 있도록 keyFile을 구성하십시오.

인증을 사용 가능하게하고 keyFile을 구성하려면 구성 파일에 다음 옵션을 추가하십시오.

```sh
keyFile = /srv/mongodb/keyfile
```

> 명령 행에서 --keyFile 옵션을 사용하여 이러한 런타임 구성 옵션을 설정할 수 있습니다.

- keyFile을 설정하면 인증이 가능하며 mongod 인스턴스가 서로 인증 할 때 사용할 키 파일을 지정합니다.
- 키 파일의 내용은 임의적이지만 배포의 모든 구성원이 동일해야 서로간에 연결할 수 있습니다.
- 키 파일의 크기는 1 킬로바이트 이하 여야하며 base64 세트의 문자 만 포함 할 수 있습니다.
- 키 파일에는 UNIX 시스템에 대한 그룹 또는 "world"권한이 없어야합니다.
 
다음 명령을 사용하여 OpenSSL 패키지를 사용하여 키 파일에 사용할 "random"컨텐트를 생성합니다.

```sh
openssl rand -base64 741
```

##### Ongoing Administration and Operation of Master-Slave Deployments
###### Deploy Master-Slave Equivalent using Replica Sets
- Master-Slave Replication과 유사한 복제 구성을 원할 경우 Replica Sets 복제 세트를 사용하여 다음 복제 구성 문서를 고려하십시오.
- 이 배포에서 호스트 <master>와 <slave> [1]은 두 인스턴스 Master-Slave 배포와 대략적으로 동일한 복제를 제공합니다.

```
{
  _id : 'setName',
  members : [
    { _id : 0, host : "<master>", priority : 1 },
    { _id : 1, host : "<slave>", priority : 0, votes : 0 }
  ]
}
```

##### Convert a Master-Slave Deployment to a Replica Set
- Master-Slave 배포를 Replica Set으로 변환하려면 현재 마스터를 단일 구성원 Replica Set로 다시 시작하십시오.
- 그런 다음 이전 slave 노드에서 데이터 디렉토리를 제거하고 새 secondary 노드로 새 복제 세트에 추가하십시오.

1. 현재 인스턴스가 마스터인지 확인하려면 다음을 실행하십시오.

```sh
db.isMaster()

{
        "ismaster" : true,
        "maxBsonObjectSize" : 16777216,
        "maxMessageSizeBytes" : 48000000,
        "localTime" : ISODate("2013-07-08T20:15:13.664Z"),
        "ok" : 1
}
```

2. 각 인스턴스에 연결된 동안 다음 명령을 사용하여 마스터 및 모든 슬레이브에서 mongod 프로세스를 종료하십시오.

```sh
db.adminCommand({shutdown : 1, force : true})
```

3. Master-Slave 로 되돌려 야 할 경우에 대비하여 / data / db 디렉토리를 백업하십시오.
4. 다음과 같이 이전 마스터를 --replSet 옵션으로 시작하십시오.

```sh
mongod --replSet <setname>
```

5. mongo 쉘을 사용하여 mongod에 연결하고 다음 명령으로 복제본 세트를 시작하십시오.

```sh
rs.initiate()
```

명령이 반환되면 한 구성원 복제 세트가 성공적으로 배포됩니다. 다음 명령을 실행하여 언제든지 복제본 세트의 상태를 확인할 수 있습니다.

```sh
rs.status()
```

##### Failing over to a Slave (Promotion)
사용할 수 없거나 손상된 마스터 (다음 예에서 A)에서 슬레이브 (B)로 영구적으로 장애 조치하려면 다음을 수행하십시오.

1. A를 종료한다.
2. B에서 mongod를 중지하십시오.
3. dbPath에서 B에서 로컬로 시작하는 모든 데이터 파일을 백업하고 이동하십시오.
4. --master 옵션을 사용하여 B에서 mongodb를 다시 시작하십시오.

> 이 작업은 한 번만 수행 할 수 있으며 되돌릴 수 없습니다. A는 전체 재 동기화를 완료 할 때까지 B의 슬레이브가 될 수 없습니다.

##### Inverting Master and Slave
> 마스터 (A)와 노예 (B)가 있고 자신의 역할을 바꾸려면이 절차를 따르십시오. 절차는 A가 건강하고 최신이며 이용 가능하다고 가정합니다.

- A가 정상이 아니지만 하드웨어가 정상인 경우 (정전, 서버 충돌 등) 1 단계와 2 단계를 건너 뛰고 8 단계에서 A의 모든 파일을 8 단계에서 B의 파일로 교체하십시오.
- A가 정상이 아니고 하드웨어가 정상이 아니라면 A를 새 시스템으로 교체하십시오. 또한 이전 단락의 지침을 따르십시오.

배포시 마스터 및 슬레이브를 반전하려면 다음을 수행하십시오.

1. fsync 명령을 사용하여 A에서 쓰기를 중지합니다.
2. B가 A의 상태로 최신인지 확인하십시오.
3. B를 종료하십시오.
4. dbPath에서 B로 시작하는 모든 데이터 파일을 백업하고 이동하여 기존 local.sources 데이터를 제거합니다.
5. B를 --master 옵션으로 시작하십시오.
6. 새로운 동기화 시작 지점을 제공하기 위해 oplog를 준비하는 B에 대한 쓰기를 수행하십시오.
7. B를 종료하면 이제 로컬로 시작하는 새로운 데이터 파일 세트가 생깁니다.
8. A를 종료하고 로컬로 시작하는 A의 dbPath에있는 모든 파일을 로컬로 시작하는 B의 dbPath에있는 파일의 사본으로 대체하십시오.
로컬 파일을 복사하는 동안 B에서 로컬 파일을 압축하는 것을 고려하십시오. 파일이 상당히 클 수도 있습니다.
9. B를 --master 옵션으로 시작하십시오.
10. 모든 일반적인 슬레이브 옵션을 사용하여 A를 시작하지만 fastsync를 포함하십시오.

##### Creating a Slave from an Existing Master’s Disk Image
> 무한 기간 동안 마스터에 대한 쓰기 작업을 중지 할 수 있으면 마스터에서 새 슬레이브로 데이터 파일을 복사 한 다음 --fastsync로 슬레이브를 시작할 수 있습니다.

fastsync와 조심하십시오. 두 인스턴스의 데이터가 동일하지 않으면 불일치가 영원히 존재하게됩니다.

- fastsync는 기존 마스터 디스크 이미지 / 백업으로 시작하여 슬레이브를 시작하는 방법입니다.
- 이 옵션은 관리자가 이미지가 정확하고 마스터의 이미지와 완전히 최신임을 보장합니다.
- 마스터로부터 데이터의 완전하고 완전한 사본을 가지고 있다면이 옵션을 사용하여 슬레이브 시작시 전체 동기화를 피할 수 있습니다.

##### Creating a Slave from an Existing Slave’s Disk Image
> 특별한 옵션없이 다른 슬레이브의 데이터 파일 스냅 샷을 복사 할 수 있습니다. 다음 경우에만 데이터 스냅 샷을 가져옵니다.

- 몽고 프로세스가 중단.
- mongod가 MMAPv1 또는 WiredTiger 스토리지 엔진에 db.fsyncLock ()을 사용하여 잠겨있을 때.

- 버전 3.2에서 변경됨 : db.fsyncLock ()은 MMAPv1 또는 WiredTiger 스토리지 엔진을 사용하여 MongoDB 인스턴스의 데이터 파일이 변경되지 않도록하여 백업 생성의 일관성을 제공합니다.
- 이전 MongoDB 버전에서 db.fsyncLock ()은 WiredTiger에 대한 저수준 백업 (예 : 파일 복사 cp, scp, tar)을위한 일관된 파일 세트를 보장 할 수 없습니다.

##### Resyncing a Slave that is too Stale to Recover
- 슬레이브는 마스터의 쓰기 작업을 비동기 적으로 적용하여 슬레이브가 마스터의 oplog에서 폴링합니다.
- oplog는 길이가 유한하고 슬레이브가 너무 늦으면 전체 재 동기화가 필요합니다.
- 슬레이브를 다시 동기화하려면, mongo를 사용하여 슬레이브에 연결하고 resync 명령을 실행하십시오

```sh
use admin
db.runCommand( { resync: 1 } )
```

> 이렇게하면 모든 데이터가 완전히 다시 동기화됩니다 (큰 데이터베이스에서는 매우 느려집니다).<br />
슬레이브에서 mongod를 중지하고 슬레이브에서 dbPath의 전체 내용을 삭제 한 다음 mongod를 다시 시작하여 동일한 효과를 얻을 수 있습니다.

##### Slave Chaining
- 슬레이브는 "chained"할 수 없습니다. 모두 마스터에 직접 연결해야합니다.
- 슬레이브가 다른 슬레이브로부터 slave from"를 시도하면 쉘의 mongod에 다음 라인이 표시됩니다.

```sh
assertion 13051 tailable cursor requested on non capped collection ns:local.oplog.$main
```

##### Correcting a Slave’s Source
> 슬레이브의 소스를 변경하려면, 슬레이브의 local.sources 콜렉션을 수동으로 변경합니다.

```sh
// 다음을 고려하십시오. 다음 예와 같이 실수로 슬레이브의 소스에 대해 잘못된 호스트 이름을 설정 한 경우 :

mongod --slave --source prod.mississippi

// --slave와 --source 인수없이 슬레이브를 재시작하여이를 수정할 수 있습니다 :
mongod

// mongo 셸을 사용하여이 mongod 인스턴스에 연결하고 다음 작업 순서대로 local.sources 컬렉션을 업데이트합니다.
use local
db.sources.update( { host : "prod.mississippi" },
                   { $set : { host : "prod.mississippi.example.net" } } )

// 올바른 명령 행 인수 나 --source 옵션없이 슬레이브를 재시작하십시오.
// 처음으로 local.sources를 구성한 후에는 --source에 후속 효과가 없다. 따라서 다음 두 가지 호출이 모두 올바른 것입니다.

mongod --slave --source prod.mississippi.example.net
or
mongod --slave

// 이제 슬레이브는 올바른 마스터에서 데이터를 폴링합니다.
```

