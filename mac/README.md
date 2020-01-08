# Mac에 관한 모든것

## ssh 생성

```sh
cd ~/.ssh
ssh-keygen -t rsa -C "$your_email"
ssh-keygen -t rsa -b 4096
ssh-keygen -f ./file-name -t ecdsa -b 521
cat ~/.ssh/id_rsa.pub
```

## host 추가하기

```sh
sudo nano /private/etc/hosts
```
> 파일 수정 후 `Ctrl + o`(저장) => `Enter`(완료) => `Ctrl + x`(나가기)

```sh
dscacheutil -flushcache
```

> 바로 적용

혹은
```sh
sudo chmod 777 /private/etc
sudo chmod 666 /private/etc/hosts
```
설정 후 `open hosts`로 열어 수정 

## VoiceOver 기능 사용하기
```sh
say -v [voice] [내용]
say [내용]
say [내용] -o [생성파일경로](.mp3, .aiff)
```

## 맥 단축키
- 크롬 전체화면: `shift` + `command` + `f`
- 크롬 캐시 지우기: `shift` + `command` + `<-(back space)`

## WebStrom
- camelCase변환: `shift` + `option` + `u`
- 관계도보기: `shift` + `option` + `command` + `u`

## 전체화면
`shift` + `command` + `F`

## 공인인증서 위치
파인더에서 `command` + `shift` + `g` 입력후 `~/library/Preferences/NPKI` 로 이동
