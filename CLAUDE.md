# aigen

## 영상 제작 규칙 (Seedance + Magnific)

모든 영상 제작 요청은 아래 흐름을 따른다.

1. **프롬프트 작성은 `seedance-clean` 스킬로 한다.** 스킬을 거치지 않고 프롬프트를 즉석에서 쓰지 않는다.
   프롬프트는 영어로, 코드블록 하나에 담아 먼저 사용자에게 보여준다.
2. **승인 후에만 생성한다.** 사용자 확인 전에는 `video_generate`를 호출하지 않는다.
3. **생성 전 `simulate_cost`로 예상 비용을 알린다.**
4. 생성 후 `creations_wait`로 완료를 확인하고, 결과 링크를 함께 전달한다.

### 기본값

| 항목 | 값 |
|---|---|
| 모델 | Seedance 2.5 (`bytedance-seedance-pro-2.5`) |
| 화면비 | 16:9 |
| 해상도 | 1080p |
| 길이 | 지정이 없으면 10초 |

모델 slug는 `video_models_list`에서 그대로 복사해 `video_generate`의 `slug`에 넘긴다.
slug를 비우면 자동 선택이 Seedance 2.0(최대 15초)을 고를 수 있으므로, 15초를 넘는 기획에서는 반드시 2.5를 명시한다.

### 길이 처리

- 4~30초: Seedance 2.5 단일 생성.
- 30초 초과: 클립으로 분할해 각각 생성한 뒤 `video_concatenate`로 합친다.
  클립 간 인물·의상·조명·시선 방향·소품 상태를 POSITIVE LOCKS에 동일하게 명시한다.

### 레퍼런스 규칙

- `keyframes.start` / `keyframes.end`와 `references[]`(image·video·character·product·style·color·effect)는 **동시에 쓸 수 없다.** 둘 중 하나만 선택한다.
- Seedance 2.5에서 end 프레임을 쓰려면 start 프레임이 반드시 함께 있어야 한다.
- 오디오는 `audioUrl`이 아니라 `references[]`에 `type: "audio"`로 넣는다.
- 로컬 파일은 서버가 채팅 첨부를 읽지 못한다. 업로드 플로우를 거치거나 Magnific 라이브러리의 기존 에셋을 쓴다.

### 프롬프트 길이

Magnific은 Seedance 2.0/2.5에서 10,000자까지 허용하지만, 안전선은 4,000자다.
초과하면 월드빌딩·환경 묘사부터 줄이고 샷 액션·카메라 값·LOCKS는 유지한다.
