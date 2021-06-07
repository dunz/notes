# Webstorm

## 기본 팁

### File Types 예외처리
Settings => file types 검색 => Text 예외처리 제거

### 코드 Inspection
파일 및 폴더 우클릭 => Inspect Code => 확인

### 오타 전체 수정
오타 클릭 => shift + F6 => 수정

## 설정

### 1. Prefer Strict mode 세팅
![strictMode](/uploads/d00f1ac236d2a356d7e425d77743c491/strictMode.png)

### 2. Line separator 세팅
![unix](/uploads/9a17f5b5f40b414243544aaaa02b709b/unix.png)
![lineSeparator](/uploads/0f425923d86bb864df96da90e06f0e99/lineSeparator.png)
![lineSeparator_oldfile](/uploads/9af42a89494ba30cef43f25b932828a6/lineSeparator_oldfile.png)
기존에 이미 다르게 설정되어진 파일은 수동으로 LF 설정을 해주어야 하고 추가되는 파일은 자동으로 세팅된 값으로 설정됩니다.

※ LF 수동설정 후 파일수정을 한번 해주어야 오른쪽 Eslint 붉은색 에러표시가 사라집니다.

이상태에서 git commit을 할 경우

```bash
warning: LF will be replaced by CRLF in config/test.js
```
커밋하려는 파일마다 해당 warning 메시지를 볼 수 있습니다.

이 문제를 해결하기 위해서는 터미널에서 git AUTO CRLF 설정 해제해야 합니다.

```bash
git config core.autocrlf false
```

### 3. Code Style 세팅
![1.TabsAndIndents](/uploads/c20ab40c03d85bb664a18eefb030542c/1.TabsAndIndents.png)
![2.Spaces](/uploads/6572c4bc837341690691284e57520b49/2.Spaces.png)
![3.WrappingAndBraces](/uploads/ebdee416ad53c7e7eeb25e5ff98894af/3.WrappingAndBraces.png)
![4.BlankLines](/uploads/9cdb47b43a0d45b5783877523021752a/4.BlankLines.png)
![5.punctuation](/uploads/b224ea3fb8e1b7ec6622ca9f13362895/5.punctuation.png)
![6.Other](/uploads/4fd6ce887191a99d308ea106f295da3e/6.Other.png)
![7.Imports](/uploads/6b773a7d01e65579b3481a6ce7d510ea/7.Imports.png)
![indent_side_setting](/uploads/5725faf11d5ed3bad3d27c41ad97ac86/indent_side_setting.png)

### 4. 코드 개행 해제
![Right_margin](/uploads/61f582a16019716f53696fd833c8db5c/Right_margin.png)
![general](/uploads/6cbfb096151424d2abba1bc9bfa84947/general.png)
![active_editor](/uploads/648fe81c8633f6819aea62332b97397b/active_editor.png)

### 5. Do not indent children tag script태그 추가
![scAutoIndenetSkip](/uploads/6aac5a08c46a5a3b6efefe60932a54e4/scAutoIndenetSkip.png)

기본 default 설정은 포맷팅시 script 태그 이후에 indentation이 한번 적용된 후 시작되는데

그럴경우 vue 파일 내부에 script태그에서 eslint 오류를 뱉기 때문에 Do not indent children 요소로 script 태그를 추가해주어야한다.

## 플러그인
### 1. CamelCase 플러그인
PascalCase, camelCase, kebab-case, snake_case 등등 case 형변환을 편하게 할수있는 플러그인

api 반환 데이터를 case변환해서 매핑시에 유용합니다.

![camelCase](/uploads/da3e5f90c0c5bd81078175b6b5f5e2ac/camelCase.png)

Shift + Alt + U 로 Case 변환 가능

### Json Viewer 플러그인
![image2018-3-19_15-21-41](/uploads/3f98624075b6635399f58a7759a80816/image2018-3-19_15-21-41.png)
![image2018-3-19_15-14-22](/uploads/7569cb59c5b27c850d35a55e43e1f8c3/image2018-3-19_15-14-22.png)

## Webstrom OS별 단축키 Manual
[WebStorm_ReferenceCard.pdf](/uploads/49668eb0292d0811eec62abdc006e6b7/WebStorm_ReferenceCard.pdf)

## 터미널 폰트설정
File -> Settings -> Editor -> Colors & Fonts -> Console Font.

> `Ctrl` + `Shift` + `a` => console font

## 터미널 변경
- bash: `C:\Program Files\Git\bin\bash.exe`
- sh:  `C:\Program Files\Git\bin\sh.exe`
- cmd: `C:\Windows\System32\cmd.exe`
- zsh: `C:\cygwin64\bin\zsh.exe`

## 콘솔 폰트 설정
Editor => Color Scheme => Console Font
- Font: Monospaced
- Size: 14
- Line spacing: 3.0

Editor => Color Scheme => Console Colors => ANSI Colors
- Black: 0A2240
- Blue: 5394EC
- Bright Blue: 7EAEF1
- Bright Cyan: 6CDADA
- Bright Green: A8C023
- Bright Magenta: FF99FF
- Bright Red: FF8785
- Bright Yellow: FFFF00
- Cyan: 299999
- Dark Gray: 555555
- Gray: 999999
- Green: 41C0AE
- Magenta: AE8ABE
- Red: FF6B68
- White: 1F1F1F
- Yellow: D67A5C

## 단축키
- 같은 객체 일괄 변경: `Shift` + `F6`
- 같은 텍스트 동시 선택: `Ctrl` + `Command` + `G`

## 유용한 확장도구
- Korean Langguage Pack
- Material Theme UI
- Atom Material Icons
- Rainbow Brackets
- Import Cost
- Console Log
- Prettier
- Better Comments
