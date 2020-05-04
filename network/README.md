# Network

## 개발서버 https 활성화하기

### ssh 키 생성
```sh
$ openssl genrsa 1024 > private.pem # 비공개키 생성
...
$ openssl req -x509 -new -key private.pem > public.pem # 공개키 생성
```
> 생성한 키는 로컬환경에서만 사용해야 하고 `*.pem` 파일은 `.gitignore`에 추가해야 한다.
