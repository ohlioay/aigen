# PRODUCT LOCK — 제품 고정 문구 라이브러리

레퍼런스 이미지만으로는 제품이 고정되지 않는다. 모델은 레퍼런스의 작은 요소를 자주 흘린다.
그래서 **레퍼런스에 이미 보이는 디테일도 프롬프트에 말로 다시 적는다.** 중복처럼 보이지만
이게 실제로 드리프트를 막는다.

## 두 군데에 나눠 적는다

### 1. ACTIVE REFERENCES — 제품이 무엇인지 서술

프롬프트 앞부분. 제품을 처음부터 정확히 인식시킨다.

```
All four attached reference images show the same garment and the same dog.
The garment: [소재+색] [카테고리] with [패턴/스티치], [보조 패널 색과 위치],
[립/트림 위치와 색], [잠금장치 종류와 경로], and [로고 탭 위치와 형태].
100% matches the references.
```

### 2. POSITIVE LOCKS — 프롬프트 맨 끝, 긍정형 고정

끝에서 한 번 더 못을 박는다. **금지형("~하지 않는다")이 아니라 긍정형**으로 쓴다.
모델은 부정문을 잘 처리하지 못해서 "no color change"가 오히려 색을 바꾸는 방아쇠가 된다.

---

## 패턴 모음

필요한 것만 골라 제품에 맞게 채운다. 전부 넣을 필요는 없다 — 그 제품에서 실제로 무너질
만한 것만 고른다.

### 컬러 블로킹

```
The [garment] keeps the same [color A] shell and the same [color B] panel
in the same position on the [부위] in every frame.
```

좌우 대칭이 아닌 제품은 위치를 명시하는 게 특히 중요하다. "ivory panel"만 쓰면 모델이
그 패널을 앞판으로 옮긴다. "ivory panel covering the flank and rear"처럼 **어디를 덮는지**
적는다.

### 부자재 (지퍼·버클·D링·스냅)

```
The same [metal/plastic] zipper line runs down the [부위], and the same
[색] [버클 종류] and [색] D-ring sit in the same positions in every frame.
```

### 로고·라벨

```
The same small woven brand tab sits low on the [부위] in every frame,
the same size and the same shape.
```

**글자를 읽히게 요구하지 않는다.** `the tab reads "PUPPIA"` 같은 문장은 모델이 문자를
그리려다 이상한 글자를 만들게 한다. 판독이 필요하면 후반 합성으로 넘긴다.

### 스티치·퀼팅 패턴

```
The same [패턴명] quilt stitching runs across the [부위] in every frame.
```

패턴명은 구체적으로. "quilted"만 쓰면 다이아몬드 퀼팅이 나온다. 어니언 퀼팅이면
"onion-shaped circular quilt stitching"처럼 형태를 서술한다.

### 립 트림 (칼라·커프·헴)

```
The ribbed [색] collar, cuffs and hem hold their shape and stay the same
[색] in every frame.
```

### 하네스 스트랩 경로

```
The harness keeps the same [색] webbing path across the [부위], the same
[색] hardware, and the same strap width in every frame.
```

하네스는 스트랩이 몸을 어떻게 감는지가 제품의 정체성이다. 경로를 적지 않으면 모델이
일반적인 하네스 모양으로 되돌린다.

### 반려견 identity

```
The dog keeps the same [모색 패턴], the same [얼굴 마킹], and the same
[귀 모양] in all shots.
```

### 씬 전체 연속성

```
One [시간대], one weather, one light direction across all shots.
The [garment] is the same garment in the same condition in every shot.
```

### 인서트 컷이 있을 때

```
The [N] inserts show the same garment from the same references, at the
same colours, with the [로고/부자재] in the same place.
```

인서트는 클로즈업이라 드리프트가 가장 눈에 띈다. 별도로 한 줄 잡아준다.

---

## 레퍼런스 역할 배분

4장을 그냥 다 넣는 것보다, 역할을 나눠 넣는 쪽이 결과가 낫다. 각도가 다른 사진을 함께
넣어야 프로필·후면 드리프트를 막을 수 있다.

| 이미지 | 역할 | 이유 |
|---|---|---|
| 앞면 제품컷 | product (제품 형태 마스터) | 지퍼·로고·정면 배치가 가장 선명 |
| 뒷면 제품컷 | image (후면 보강) | 후면 패널과 스트랩 경로 |
| 착용샷 A | character (반려견 identity) | 실제 착용 핏 |
| 착용샷 B | character (각도 보강) | 다른 각도로 드리프트 방지 |

`product` 타입은 보통 1장 제한이므로 가장 선명한 한 장을 고른다. 나머지는 `image` 또는
`character`로 넣는다. 실제 타입 이름과 개수 제한은 쓰는 모델의 스키마를 확인한다.

---

## 결과 검토 체크리스트

최종본을 받으면 원본 4장과 나란히 놓고 대조한다. 눈으로 확인하지 않은 항목을 확인한 것처럼
보고하지 않는다.

- [ ] 컬러 배치가 같은 위치인가 (좌우 반전 포함)
- [ ] 지퍼·버클·D링이 같은 자리, 같은 색인가
- [ ] 로고·라벨 탭이 같은 쪽, 같은 크기인가
- [ ] 봉제선·퀼팅이 같은 무늬인가
- [ ] 립 트림 색이 셸과 구분되게 유지되는가
- [ ] 스트랩 경로와 폭이 같은가
- [ ] 인서트 컷에서도 위 항목이 유지되는가
- [ ] 반려견 마킹·귀 모양이 샷 사이에서 일정한가
