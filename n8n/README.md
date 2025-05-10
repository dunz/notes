# n8n

## n8n 스타터 킷으로 로컬에 설치하여 빠르게 시작
https://github.com/n8n-io/self-hosted-ai-starter-kit
```
git clone https://github.com/n8n-io/self-hosted-ai-starter-kit.git
cd self-hosted-ai-starter-kit
docker compose up
```

## 텔레그램 봇 chat id 확인하는 법
텔레그램을 전송하려면 chat id가 필요한데 확인하기 까다롭다  
아래 주소를 접속한 상태에서 챗봇에 메시지를 보내면 확인 가능하다

## Gmail 어카운트 추가하기
[https://console.cloud.google.com/](https://console.cloud.google.com/apis/credentials?inv=1&invt=Abw_Tg&project=n8nproject-458807)



```
https://api.telegram.org/bot(봇token)/getUpdates
```

## 자체 호스팅 후보 목록
- https://fly.io/
- https://render.com/
- https://railway.com/
- https://www.coolify.io/

<img width="767" alt="image" src="https://github.com/user-attachments/assets/44717466-c41e-44aa-a1e7-e00128c23105" />

## fly.io 로 n8n 셀프호스팅 하기

### 1. fly cli 설치하기
```
brew install flyctl 
```

### 2. fly 프로젝트 초기화
```
mkdir n8n
cd n8n
flyctl launch --no-deploy # 배포 없이 초기화
```

### 3. 생성된 toml 파일에 n8n 구성 추가

```
# fly.toml app configuration file generated for n8n-dunz on 2025-05-05T16:23:50+09:00
#
# See https://fly.io/docs/reference/configuration/ for information about how to use this file.
#

app = 'n8n-dunz'
primary_region = 'nrt'  # 일본 도쿄 리전

[build]
  image = 'docker.n8n.io/n8nio/n8n:latest'  # n8n 도커 공식 이미지

[env]
  EXECUTIONS_DATA_MAX_AGE = '60'
  EXECUTIONS_DATA_PRUNE = 'true'
  GENERIC_TIMEZONE = 'Asia/Seoul'
  N8N_EDITOR_BASE_URL = 'https://n8n-dunz.fly.dev'
  N8N_HOST = 'n8n-dunz.fly.dev'
  N8N_PORT = '5678'
  N8N_PROTOCOL = 'https'
  N8N_SECURE_COOKIE = 'true'
  WEBHOOK_URL = 'https://n8n-dunz.fly.dev'

[[mounts]]
  source = 'n8n_data'
  destination = '/home/node/.n8n'

[http_service]
  internal_port = 5678
  force_https = true
  auto_stop_machines = 'stop'
  auto_start_machines = true
  min_machines_running = 1

[[vm]]
  memory = '512'  # n8n은 최소 512는 되어야 돌아감
  cpu_kind = 'shared'
  cpus = 1

```

#### 4. fly app 다시 생성
toml 기반으로 fly app 다시 생성

```
flyctl launch
```

#### 5. fly 볼륨 추가
```
flyctl volumes list --app n8n-dunz
```

#### 6. fly 배포
```
flyctl deploy
```

