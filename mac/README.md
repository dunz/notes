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