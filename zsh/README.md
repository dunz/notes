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
bash: /etc/bash.bashrc 파일 수정
zsh: ~/.zshrc 파일 수정

#### 환경변수 해제
```sh
unset [:]
```
