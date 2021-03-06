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

## oh-my-zsh 플러그인 추가하기
### install plugin
zsh-syntax-highlighting
```sh
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting
```

zsh-autosuggestions
```sh
git clone https://github.com/zsh-users/zsh-autosuggestions.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-autosuggestions
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
