# aigen

## 영상 제작 규칙 (Seedance + Magnific)

모든 영상 제작 요청은 아래 흐름을 따른다.

1. **프롬프트 작성은 `seedance-clean` 스킬로 한다.** 스킬을 거치지 않고 프롬프트를 즉석에서 쓰지 않는다.
   프롬프트는 영어로, 코드블록 하나에 담아 먼저 사용자에게 보여준다.
2. **승인 후에만 생성한다.** 사용자 확인 전에는 `video_generate`를 호출하지 않는다.
3. **생성 전 `simulate_cost`로 예상 비용을 알린다.**
4. 생성 후 `creations_wait`로 완료를 확인하고, 결과 링크를 함께 전달한다.

### 최종본 기본값 (확정)

사용자가 명시적으로 바꾸라고 하기 전까지 아래 값을 그대로 쓴다.

| 항목 | 값 |
|---|---|
| 모델 | Seedance 2.5 |
| 해상도 | 1080p |
| 길이 | **20초** |
| 샷 수 | **6샷** |
| 사운드 | **끔 — 영상만 생성한다** |
| 화면비 | 채널에 맞춰 결정 (릴스·틱톡·쇼츠 9:16 / 웹 히어로·유튜브 16:9) |

Higgsfield에서는 `seedance_2_5` + `mode: "omni_reference"` + `generate_audio: false`.
`generate_audio`는 기본값이 `true`이므로 **명시하지 않으면 오디오가 붙는다.**
1080p 20초 = 180크레딧 (9크레딧/초, 길이에 완전 비례).

### 샷 구성 (최종본에서 가장 중요한 작업)

세팅이 고정됐으므로 판단할 것은 **6개 샷을 어떻게 짜는가** 하나다. 두 목표를 동시에 만족시킨다.

1. **시청자가 지루하지 않을 것.** 6개 샷이 서로 다른 구도·카메라 무브·속도를 갖는다.
   슬로우모션·돌리줌·패닝·탑뷰·줌인·줌아웃·트래킹·오빗·랙포커스 중에서 **제품의 어느 면을
   보여줄지에 따라** 골라 조합한다. 카메라 높이를 위아래로 흔들고, 슬로우는 히어로 액션
   한 구간에만 건다. 시간은 균등 배분하지 않는다.
2. **제품이 효과적으로 보일 것.** 샷을 다 짠 뒤 고정 대상을 하나씩 짚으며 "이건 몇 번 샷에서
   보이는가"를 답한다. 답이 없는 항목이 있으면 샷 구성을 바꾼다. 측면 샷만 여섯 개면
   정면 D링과 등 지퍼는 영상에 한 번도 안 나온다.

기법별 카드(무엇을 보여주는가·FOV·프롬프트 문구·주의점)와 20초 6샷 기본 골격은
`.claude/skills/video_gen/references/camera-techniques.md`에 있다. 최종본 프롬프트를 쓰기 전에 읽는다.

⚠️ 돌리줌은 제품 실루엣을 왜곡하므로 제품이 크게 잡히는 샷에는 쓰지 않는다.
⚠️ 오빗·탑뷰는 레퍼런스에 없는 각도를 지나가면 드리프트가 난다. 해당 각도 제품컷이 있는지 먼저 본다.

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
- **`references[]`에 `modifier_key`를 넣지 않는다.** 프롬프트의 `@tag`를 특정 레퍼런스에 연결하려고 쓰는 필드지만, Seedance 일반 레퍼런스에서는 동작하지 않고 작업이 큐 직후 `failed`로 죽는다(`failureReason: unknown`). 실패 기록의 `mediaCollection`이 비어 있으면 레퍼런스가 하나도 붙지 않은 것이므로 이 경우를 의심한다. `custom_model_id`를 쓰는 커스텀 모델 전용 필드로 취급한다.
- 따라서 프롬프트에 `@image1` 같은 태그를 쓰지 않는다. 연결 기제가 없어 리터럴 텍스트로 남고, 모델이 화면에 글자로 그리려 할 수 있다. `references[]`는 순서대로 전달되므로 "All five attached reference images show the same dog and the same garment"처럼 **묶어서 지칭**하고, 고정할 디테일은 텍스트로 서술한다.
- 레퍼런스는 `url`에 creation identifier를 그대로 넣는다(`webUrl` 아님).
- 생성 후 `creations_get`으로 `mediaCollection`에 `type: reference`가 넣은 장수만큼 있는지 확인한다. 이게 레퍼런스가 실제로 붙었는지 검증하는 유일한 방법이다.
- 실패한 작업은 크레딧이 차감되지 않는다. 다만 30초 본생성 전에 2.0 Mini / 480p / 4초(수백 크레딧)로 레퍼런스 첨부만 먼저 검증하면 시간을 아낄 수 있다.

### 프롬프트 길이

Magnific의 `video_models_list`는 Seedance 2.0/2.5의 `prompt.maxLength`를 10,000자로 보고한다.
목표는 4,000자 안팎이되, 제품 고정용 ACTIVE REFERENCES와 POSITIVE LOCKS를 깎아서 맞추지는 않는다.
줄여야 하면 월드빌딩·환경 묘사부터 줄이고 샷 액션·카메라 값·LOCKS는 유지한다.
