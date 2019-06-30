# Mongo Cheat Sheet

## 서버시작1
### 1. mongoDB 설치 경로 환경변수로 추가
### 2. Storage Directory 생성
```sh
mkdir d:\Projects\blabla    // 사용자 지정 디렉토리
or
sudo mkdir -p /data/db  // MongoDb Default Storage 경로를 생성해줌
// 현재 user가 권한이 없을경우 권한도 생성
whoami
sudo chwon username /data/db
```
### 3. Storage 지정하여 서버시작
```sh
mongod --dbpath d:\Projects\blabla
or
mongod  // 이렇게 되면 기본 Default data경로로 시작(/data/db)
```

## 서버시작2
### 1. directory생성
```sh
$ mkdir d:\Projects\storage
$ mkdir d:\Projects\staroge\data
$ mkdir d:\Projects\storage\log
```
### 2. conf 파일 생성
```sh
$ notepad d:\Projects\storage\mongod.conf
```
### 2. conf 파일 생성2
```sh
mongodb.conf

mongod --config c\mongodb\mongodb.conf --install   // --install 서비스로 등록되고 다음부터는 생략 가능

// 파일내용
dbpath=c:\mongodb\data\             // data경로
logpath=c:\mongodb\log\logs.log     // log경로
logappend=true
rest=true
directoryperdb=true
```

### 3. directory생성후 conf파일 작성(내용 테스트해보지 않음)
systemLog:
	destination: file
	path: D:\Projects\storage\log\mongod.log
	logAppend: true

storage:
	dbPath: D:\Projects\storage\data
	directoryPerDB: true

jounal:
	enabled: true

processManagement:
	fork: true

net:
	port:40001
### 4. conf파일을 이용하여 인스턴스 시작
```sh
$ mongod --config d:\Projects\storage\mongod.conf
```

## 서버접속
### 1. mongo localhost:27017(server port)

## 클라이언트에서 서버종료
```sh
$ use admin
$ db.shutdownServer() 

```

## 클라이언트 문법
```sh
// db스키마 확인
db.stats()

// 호스트확인
db.hostInfo()

// db보기
show dbs

// db이동or생성(collection 생성 시점에 생성됨)
use test

// collections보기
show collections

// collection생성
db.createColection('emp', {capped:true, size:400000}) // capped collection
db.createColection('emp', {name:'dongju', age:'31'}) // non capped collection (default)

// collection 이름변경
db.emp.renameCollection('employees')
1
// collection 삭제
db.emp.drop()

// document 추가
db.emp.insert({name:'dongju', age:'31'})

// document 추가2
db.emp.save({name:'dongju', age:'31'})

// document 전체수정
db.emp.update({age:'31'}, {age:'32'})

// document field 일부 추가 및 수정
db.emp.update({age:'31'}, {$set:{age:'32'}})

// document field 일부 제거
db.emp.update({age:'31'}, {$unset:{age:'32'}})

// array field에서 빼내기
db.emp.update({age:'31'}, {$pull:{age:'32'}})

// array field 에 넣기
db.emp.update({age:'31'}, {$push:{age:'32'}})

// 해당 document 삭제
db.emp.remove({age:'31'});

// 전체 document 삭제
db.emp.remove({});

// document 찾기
db.emp.find({age:'31'})   // where
db.emp.find()             // all

// document 하나 찾기
db.emp.findOne({age:'31'})    // where
db.emp.findOne()              // all

// 검색결과 변수에 할당
var cursor = db.emp.find()
while(cursor.hasNext()) printjson(cursor.next())    // 배열데이터를 반복문을 통해 출력
 
var cursor = db.emp.find()
printjson(cursor[2])    // 해당 index의 결과만 출력

db.emp.find().pretty()       // pretty 하게 표현
db.emp.find().toArray          // 배열로 표현
db.emp.find().toArray()[2]    //  이렇게도 표현 가능

db.emp.update{{_id:ObjectId("58ead063809353539623ddfc")}, {name:'gindong'}}     // update emp set name='gindong' where _id=ObjectId("58ead063809353539623ddfc");
db.emp.save({_id:ObjectId("58ead063809353539623ddfc"), name:'haha'})        // 해당 키에 매치되는 document 새로 저장(기존필드덮어씌움) / 매치안될경우 insert

db.emp.find({no:{gt:7500, $lte:7600}}, {_id:0, no:1, name:1})   // select no, name from emp where no>7500 and no<=7600;

db.emp.count()  // select count(*) from emp;
db.emp.find({no:{$gt:7900}}).count(*)   // select count(*) from emp where no>7900;

db.emp.find({$or:[{no:1}, {no:2}]}, {_id:0, no})   // select no from emp where no=1 or no=2;
db.emp.find({no:{$in:[1, 2]}}, {_id:0, no}) // select no from emp where no in (1,2);

db.emp.find({$and:[{no:1}, {name:{$gt:1000}}]}, {_id:0, no:1, name:1})      // select no, name from emp where no=1 and name>1000;
db.emp.find({{no:1, name:{$gt:1000}}}, {_id:0, no:1, name:1})      // 위와동일 $and일 경우에만 생략 가능

db.emp.find({no:{$exist:true}}, {_id:0, name:1})    // no값이 존재하는 document만 조회
db.emp.find({no:{$exist:false}}, {_id:0, name:1})    // no값이 존재하지 않는 document만 조회

db.emp.distinct('no');  // select distinct no from emp;

db.emp.find({no:1}, {_id:0, name:1, no:1}).sort(name:-1)    // select name, no from emp where no=1 order by name desc;
```

## 마스터 슬레이브 생성
1. Primary DB가 저장될 홈 경로 생성 ex) /data/master
2. Secondary DB가 저장될 홈 경로 생성 ex) /data/slave1
3. Secondary2 DB가 저장될 홈 경로 생성 ex) /data/slave2

```sh
// master 생성
mongod --dbpath /data/master --port 20000 --master

// slave1 생성
mongod --dbpath /data/slave1 --port 20001 --slave --source localhost:20000

// slave2 생성
mongod --dbpath /data/slave2 --port 20002 --slave --source localhost:20000

// master 접속
mongo localhost:20000

// slave1 접속
mongo localhost:20001

// slave2 접속
mongo localhost:20002
```

## 리플라카셋 생성
```sh
mongod --dbpath /data/primary --replSet downSet --port 20000
mongod --dbpath /data/secondary1 --replSet downSet --port 20001
mongod --dbpath /data/secondary2 --repletSet downSet --port 20002

mongo localhost:20000
var config = {_id:'downSet', members:[
    {_id:0, host:'localhost:20000'},
    {_id:1, host:'localhost:20001'},
    {_id:2, host:'localhost:20002'}
]};
rs.initiate(config);    // 리플리카셋 초기화 동기화 시작 
```



