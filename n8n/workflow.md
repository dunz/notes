# Workflow

## 유튜브 숏츠 자동 발행 워크플로우

### 툴
- LLM
- fal.ai

### 작업 실행
1. 숏츠 기획 프롬프트
```
너는 자동화 유튜브 쇼츠 콘텐츠 기획 전문가야.
저작권에 문제가 없는 고전 이야기 • 역사 • 신화 • 민담 • 철학적 딜레마 등을 바탕으로 5가지 주제를 추천해줘.

출력 형식:
- 코드 블록(```) 안에 탭(\t) 으로 정확히 구분된 텍스트를 제공해줘
- 각 행은 다음 순서로: Subject, Narration, Status, Caption, Publish
- 헤더 없이, 바로 데이터 행만 5개 제공

지침:
- Subject: 후킹 요소를 넣어 5~7단어 이내로 작성
- Narration: 30초 분량(약 60~70단어)의 나레이션
- Status: 항상 준비
- Caption: 10줄 분량의 심층 부연 설명, 줄바꿈 없이 셀 하나에 들어가도록 작성
- Publish: 항상 대기중
```

2. 나온 결과물 구글 시트에 추가하기
3. n8n 에서 구글 시트 get row 로 가져오기
  - google drive, google, sheets api 사용 활성화
    - https://console.cloud.google.com/apis/library?inv=1&invt=AbyUTA&project=n8nproject-458807
4. 가져올 시트를 선택후 필터 추가
  - Status가 준비인것 필터 추가
  - return on first matching row 옵션 추가
5. 나레이션 생성
```
Method: POST
URL: https://queue.fal.run/fal-ai/elevenlabs/tts/turbo-v2.5

Header1:
Name: Authorization
Value: Key $FAL_AI_API_KEY

Header2:
Name: Content-Type
Value: application/json

JSON:
{
  "text": "{{ $json.Narration }}",
  "language_code": "ko"
}
```
- fal.ai: https://fal.ai/ - 멀티모달 모델을 웹이나 API를 통해 바로 사용할
