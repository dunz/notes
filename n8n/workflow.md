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
5. 나레이션 생성을 위한 elevenlabs api 호출
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
  - https://fal.ai/models/fal-ai/elevenlabs/tts/turbo-v2.5
  - elevenlabs 의 turbo 2.5 모델 사용 (호출당 0.01달러 차감)
  - 추후 가격 최적화를 위해 elevenlabs api 를 직접 연동하는것을 고려(초기 1달러 제공)
    - 학습용/테스트용: Fal.ai 경유 사용도 무방
    - 서비스/제품화 목적: ElevenLabs API 직접 사용하는 것이 훨씬 좋음 (비용, 성능, 유연성 면에서 유리)
- api 키와 헤더 및 바디를 설정
6. wait 노드로 초 지정하여 음성 생성 기다리기
7. 생성한 나레이션 음성 가져오기
```
Method: GET
URL: https://queue.fal.run/fal-ai/elevenlabs/requests/{{ $json.request_id }}
```
8. 이미지 영상 생성하기
  - Basic llm chain 노드 사용
    - 모델, output 형식 지정
```
// prompt

주어진 스크립트에 따라 매우 몰입감 있고 영화적인 장면 프롬프트를 5장 생성해 주세요.

요구사항:
1. 생생하고 감각적인 동작 중심 묘사를 사용할 것 (예: 움켜잡다, 밀치다, 달리다, 쓰러지다 등)
2. 스크립트에 따라 논리적인 순서로 전개될 것
3. 평범한 일상보다는 독특하고 극적이며 시각적으로 강렬한 장면 위주로 구성 할 것
4. 몰입감을 높이기 위해 시네마틱 샷 등의 키워드를 포함할 것
5. 각 프롬프트는 200자 이내로 상세하게 작성
6. 큰따옴표("")는 사용하지 않음
7. 프롬프트는 영어로 생성함
```
9. 이미지 생성을 위한 나레이션으로 장면 생성용 프롬프트 5개 생성하기
  - gemini 2.5 flash 모델 사용
  - output 으로 Item List Output Parser 사용
11. 만든 프롬프트로 이미지 생성 api 호출 (Recraft)
```
Method: POST
URL: https://queue.fal.run/fal-ai/recraft-v3

Header1:
Name: Authorization
Value: Key $FAL_AI_API_KEY

Header2:
Name: Content-Type
Value: application/json

JSON:
{
  "prompt": "{{ $json.text }}",
  "image_size": "portrait_16_9",
  "style": "digital_illustration/neon_calm"
}
```
12. api 응답 대기를 위한 delay 주기
13. 이미지 url 가져오기
```
Method: GET
URL: https://queue.fal.run/fal-ai/recraft-v3/requests/{{ $json.request_id
```
14. 영상 생성하기
```
Method: POST
URL: https://queue.fal.run/fal-ai/kling-video/v1.6/standard/image-to-video

Header1:
Name: Authorization
Value: Key $FAL_AI_API_KEY

Header2:
Name: Content-Type
Value: application/json

JSON:
{
  "prompt": "{{ $( 'Image Prompt').item.json.text }}",
  "image_url": "{{ $json.images[0].url }}",
  "duration": "5",
  "aspect_ratio": "9:16",
  "negative_prompt": "blur, distort, and low quality"
}
```

15. 영상 생성시까지 대기하기
16. 영상 주소 가져왹
```
Method:
GET
URL: https://queue.fal.run/fal-ai/kling-video/requests/{{ $json.request_id }}
```

  
