# 🐳 Docker 필수 명령어 치트시트

## 기본 정보 확인
- `docker --version` → 도커 버전 확인  
- `docker info` → 도커 환경 정보 요약 확인  
- `docker help` → 명령어 도움말  

---

## 📦 이미지 (Images)
- `docker images` → 로컬 이미지 목록  
- `docker pull <이미지명>:<태그>` → 이미지 다운로드  
- `docker rmi <이미지ID>` → 이미지 삭제  
- `docker build -t <이름>:<태그> .` → Dockerfile로 이미지 빌드  

---

## 🚀 컨테이너 (Containers)
- `docker ps` → 실행 중인 컨테이너 확인  
- `docker ps -a` → 모든 컨테이너 확인 (종료 포함)  
- `docker run -it --name <컨테이너명> <이미지명>` → 새 컨테이너 실행 (대화형)  
- `docker start <컨테이너명>` → 중지된 컨테이너 시작  
- `docker stop <컨테이너명>` → 컨테이너 중지  
- `docker restart <컨테이너명>` → 재시작  
- `docker rm <컨테이너명>` → 컨테이너 삭제  

---

## 🔍 컨테이너 내부 확인
- `docker logs <컨테이너명>` → 로그 확인  
- `docker exec -it <컨테이너명> /bin/bash` → 컨테이너 내부 접속  
- `docker inspect <컨테이너명>` → 상세 설정 및 상태 확인  

---

## 📂 볼륨 & 네트워크
- `docker volume ls` → 볼륨 목록  
- `docker volume rm <볼륨명>` → 볼륨 삭제  
- `docker network ls` → 네트워크 목록  
- `docker network create <네트워크명>` → 네트워크 생성  

---

## 🧹 정리 (Cleanup)
- `docker system prune` → 사용하지 않는 컨테이너/이미지/네트워크 정리  
- `docker image prune` → 사용하지 않는 이미지 삭제  
- `docker container prune` → 중지된 컨테이너 삭제  

---

## 🛠️ 도커 컴포즈
- `docker-compose up -d` → 서비스 실행 (백그라운드)  
- `docker-compose down` → 서비스 중지 및 리소스 정리  
- `docker-compose logs -f` → 서비스 로그 실시간 확인  
