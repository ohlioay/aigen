# 스타일 프리셋

## 브랜드 기본 — 2000년대 35mm 필름룩 (코닥 계열, 약하게)

퍼피아 화보 영상의 **기본 그레이딩**이다. 사용자가 다른 톤을 지정하지 않으면 이걸 쓴다.

### 왜 "Kodak Portra"라고 쓰지 않는가

프롬프트에 필름 브랜드·기종 이름을 적으면 대개 무시되거나, 모델이 그 이름에서 엉뚱한 스타일을
끌어온다. seedance-clean이 장비 이름을 금지하는 것과 같은 이유다. 대신 **그 필름이 화면에서
실제로 만들어내는 물리적 결과**를 서술한다. 결과는 더 정확하고, 강도 조절도 말로 할 수 있다.

### 프롬프트에 넣을 문구

`STYLE` 블록(기술 스타일 서픽스)에 넣는다:

```
Shot on 35mm motion picture film. Fine visible grain across the whole frame,
slightly heavier in the shadows. Gentle highlight roll-off with soft halation
blooming around the brightest speculars. Warm amber bias in the highlights,
creamy mid-tones, shadows lifted a little and leaning warm rather than blue.
Contrast is soft, not crushed. A mild natural vignette at the corners.
Colours stay true to the garment — the film character sits on top of accurate
product colour rather than replacing it.
```

마지막 문장이 이 프리셋의 핵심이다. 필름 그레이딩은 색을 밀어내는 성질이 있어서, 그냥 두면
제품 실색이 따라 움직인다. **필름 캐릭터는 얹되 제품 색은 유지**하라고 명시해야 오렌지가
붉게, 베이지가 누렇게 가는 걸 막을 수 있다.

### 강도 조절

사용자가 "너무 강하다 / 약하다"고 하면 이 순서로 만진다.

| 요청 | 바꿀 것 |
|---|---|
| 더 약하게 | `Fine visible grain` → `very fine grain, barely visible`; halation 문장 삭제 |
| 더 강하게 | `Fine` → `pronounced`; `mild natural vignette` → `noticeable vignette`; 셔터 관련 모션블러 추가 |
| 색만 더 따뜻하게 | `warm amber bias` → `strong amber bias in the highlights` |
| 색만 더 중립적으로 | `warm amber bias in the highlights, creamy mid-tones` → `neutral highlights with a faint warm lift` |

### 반드시 알려야 할 트레이드오프

**필름 그레인은 미세 디테일을 부드럽게 만든다.** 봉제선, 퀼팅 스티치, 로고 탭의 가장자리가
클린 디지털룩보다 덜 또렷해진다. 화보·브랜딩용이면 문제없지만, **상세페이지에서 원단 질감이나
바느질을 보여주는 게 목적이라면 이 프리셋은 맞지 않는다.** 기획서에서 이 점을 먼저 짚고,
용도를 확인한 뒤 진행한다.

절충안: 인서트 클로즈업 구간만 그레인을 낮춘다고 프롬프트에 적을 수는 있지만, 한 생성 안에서
구간별 그레인 차이는 잘 지켜지지 않는다. 디테일이 정말 중요하면 **인서트를 별도 생성으로 빼서
클린하게 뽑고 편집에서 붙이는 편**이 확실하다.

---

## 대안 프리셋

사용자가 다른 톤을 원할 때 제시한다.

| 프리셋 | 프롬프트 방향 | 어울리는 용도 |
|---|---|---|
| 내추럴 데이라이트 | 중립 화이트밸런스, 정확한 색, 그레인 없음, 선명한 디테일 | 상세페이지, 커머스 |
| 쿨 시네마틱 | 청록 계열 그림자, 낮은 채도, 강한 명암 대비 | 테크·아웃도어 브랜딩 |
| 파스텔 소프트 | 밝은 노출, 낮은 대비, 부드러운 확산광 | 반려동물 감성 콘텐츠 |
| 2000년대 35mm 필름룩 | 위 기본 프리셋 | 화보, 브랜드 필름 |
