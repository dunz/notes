## 쉘 명령어

### 파일내용 클립보드에 복사하기
```sh
pbcopy < [filename]
```

### 파일내용 파일에 복사하기
```sh
cat .zshrc > test.txt
```

### 현재 디렉토리 열기
```sh
open .
// start . (window)
```

### 기본 쉘 변경
```sh
chsh -s `which zsh`
```

### 인스턴스 확인 (웹팩) 및 죽이기
```sh
ps -ef | grep webpack
501 38704     1   0  1:59PM ??         3:00.16 /usr/local/bin/node /Users/djlee/local/test/node_modules/.bin/webpack-dev-server --config config/v2/webpack.config.dev.js --open
  501 51432     1   0  4:33PM ??         0:25.88 /usr/local/bin/node /Users/djlee/local/test/node_modules/.bin/webpack-dev-server --config config/v2/webpack.config.dev.js --open

kill 38704
```

## oh-my-zsh 플러그인 추가하기
### Iterm2 설치하기
https://iterm2.com/

### homebrew 설치하기
```sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

### zsh 설치하기
```sh
# zsh 설치하기
brew install zsh

# zsh 설치경로 확인하기
which zsh

# 기본 shell 변경하기
chsh -s $(which zsh)
```

### oh-my-zsh 설치
```sh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"

```

### .zshrc 붙여넣기
`open ~/.zshrc`
zshrc: https://gist.github.com/dunz/afef46f8fdaf81fc64985dae22a4f9ab

### 한글폰트 설치
D2폰트
https://github.com/naver/d2codingfont

### Item2 환경설정 변경
Preference => Profile => Text => Font 변경

### install plugin
zsh-syntax-highlighting
```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting
```

zsh-autosuggestions
```sh
git clone https://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions
```

fzf
```sh
git clone --depth 1 https://github.com/junegunn/fzf.git ~/.fzf && ~/.fzf/install
```

### add plugin
```
plugins=(
	git
	dnf
	zsh-syntax-highlighting
	zsh-autosuggestions
)
```

### 환경변수 추가하기

#### 환경변수 현재 터미널 세션에 설정

```sh
export [:변수명]=[:값]		// linux
set [:변수명]=[:값]			// window
```

#### 환경변수 영구 설정
- bash: /etc/bash.bashrc 파일 수정
- zsh: ~/.zshrc 파일 수정

#### 환경변수 해제
```sh
unset [:변수명]
```

## 윈도우에서 zsh 설치 절차
1. windows subsystem enable하기
	1. 윈도우 검색 powershell 실행
	2. state 확인: `Get-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`
	3. disable일 경우 enable설정: `Enable-WindowsOptionalFeature -Online -FeatureName Microsoft-Windows-Subsystem-Linux`
	4. 재시작
2. ubuntu 설치하기
  > window store 에서 linux검색 후 ubuntu설치 실행 
3. zsh 설치
  > `sudo apt-get install zsh` 명령어 실행
4. Oh My Zsh 설치
  > `sh -c "$(curl –fsSL https://raw.githubusercontent.com/robbyrussell/oh-my-zsh/master/tools/install.sh)"`
5. Powerline fonts 설정
  1. https://github.com/powerline/fonts
  2. fonts-master --- fonts-master
  3. Powershell as an Admin
  4. Stand down the execution policy:
  5. Set-ExecutionPolicy Bypass --- yes
  6. .\install.ps1
  7. Set-ExecutionPolicy Default

#### npm install 권한 에러시
```sh
sudo chown -R $(whoami) /Users/djlee/Work/[현재디렉토리]

```

### 트리구조의 폴더구조 텍스트로 뽑기
```
// tree 가 없다면 먼저 설치
berw install tree

// -I : 제외 설정
// sed : NBSP 로 공백이 생성되는데 이걸 '' 문자열로 치환해줌
// > : 결과물 파일로 생성
tree -I 'node_modules|dist|images|animations|*.json|*.pem|*.md' | sed 's/\xC2\xA0/ /g' > structure.txt

```
