### npm 명령어
```sh
// npm 모든 사용법 리스트
npm -l

// npm 버전 확인
npm version
npm -v // 간소화

// npm 초기화
npm init [--force|-f|--yes|-y]

// npm config 활용
npm config -h
npm c
npm c list 대표 config보기
npm c ls -l 모든 config보기
npm c edit config edit 모드

// 패키지 인스톨
npm install [패키지][@tag|@version] | <folder> | <git:// url> | <github username>/<github project>
npm i
npm i [--save-dev|-S|--save-dev|-D]

// 패키지 언인스톨
npm uninstall [패키지][@version]
npm un
npm un [--save-dev|-S|--save-dev|-D]

// 패키지 검색
npm search [패키지] [--long]
npm s

// 찾고자 하는 패키지 package.json 확인
npm view [패키지][@version]
npm v

// 런스크립트 실행
npm run-script [커맨드] [--args]
npm run

// 패키지 문서보기
npm docs [패키지]

// 패키지 버그 및 이슈 보기
npm bugs [패키지]

// 패키지 중복제거
npm dedupe
npm ddp

// cache 확인 및 제거
npm cache verify
npm cache clean --force

// 만료(최신버전이 올라간) 패키지 확인
npm outdated        // 프로젝트 설치 패키지
npm outdated -g     // 전역 설치 패키지
```