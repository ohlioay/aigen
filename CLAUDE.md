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

### 제품 일관성 (최우선 규칙)

강아지가 착용·사용하는 제품은 **어떤 경우에도 변형하지 않는다.** 레퍼런스 사진에 있는 그대로 유지한다.

고정 대상: 색상 배치(컬러 블로킹 위치까지), 실루엣·재단·기장, 봉제선·퀼팅 패턴, 지퍼·버클·D링 등 부자재의 위치와 색, 브랜드 로고·라벨의 위치와 형태, 하네스 스트랩 경로와 폭.

지키는 방법:

- 레퍼런스는 `references[]`에 `type: "product"`(제품 형태 고정) + `type: "character"`(반려동물 identity 고정)로 나눠서 넣는다. 각도가 다른 사진을 함께 넣어 프로필·후면 드리프트를 막는다.
- 레퍼런스에 이미 보이는 디테일이라도 **프롬프트 텍스트에 한 번 더 말로 적는다.** 모델이 레퍼런스의 작은 요소를 흘리는 일이 잦다.
- `POSITIVE LOCKS`에 제품 고정 문구를 긍정형으로 넣는다.
  예: `the quilted bomber keeps the same olive shell, ivory side panel, ribbed olive collar and cuffs, same zipper line and the same brand tab in the same position in every frame.`
- 로고 텍스트는 글자로 읽히도록 설계하지 않는다. Seedance는 생성된 글자를 뭉갠다. 로고는 **위치와 형태(작은 라벨 탭)** 로만 고정하고, 판독이 필요한 로고는 후반 합성으로 처리한다.
- 결과를 받으면 컬러 배치·부자재 위치·로고 위치를 레퍼런스와 대조해 확인한 뒤 전달한다.

### 레퍼런스 규칙

- `keyframes.start` / `keyframes.end`와 `references[]`(image·video·character·product·style·color·effect)는 **동시에 쓸 수 없다.** 둘 중 하나만 선택한다.
- Seedance 2.5에서 end 프레임을 쓰려면 start 프레임이 반드시 함께 있어야 한다.
- 오디오는 `audioUrl`이 아니라 `references[]`에 `type: "audio"`로 넣는다.
- 로컬 파일은 서버가 채팅 첨부를 읽지 못한다. 업로드 플로우를 거치거나 Magnific 라이브러리의 기존 에셋을 쓴다.

### 프롬프트 길이

Magnific의 `video_models_list`는 Seedance 2.0/2.5의 `prompt.maxLength`를 10,000자로 보고한다.
목표는 4,000자 안팎이되, 제품 고정용 ACTIVE REFERENCES와 POSITIVE LOCKS를 깎아서 맞추지는 않는다.
줄여야 하면 월드빌딩·환경 묘사부터 줄이고 샷 액션·카메라 값·LOCKS는 유지한다.
