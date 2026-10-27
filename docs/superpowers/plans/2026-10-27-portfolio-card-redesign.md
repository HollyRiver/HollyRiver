# 포트폴리오 SurvLLM 카드 재구성 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** `hollyriver.github.io`의 SurvLLM 프로젝트 카드를 `이미지 → 개요(Phase 1·2) → 기여` 2항목 구조로 재구성하고, 제목 줄 뱃지 배치를 네 카드 전체에 걸쳐 정리한다.

**Architecture:** 빌드 도구 없는 정적 사이트. `css/style.css`에 범용 CSS(`figure.fig`, `.phase`/`.pnum`)를 추가하고 `index.html`의 첫 카드 본문만 교체한다. 나머지 세 카드의 HTML 문구는 손대지 않는다. 검증은 자동화 테스트가 아니라 헤드리스 Chrome 실측(`.debug/measure-print.mjs`)과 브라우저 육안 확인으로 한다.

**Tech Stack:** HTML5 `<details>`, 순수 CSS, Node 기반 CDP 스크립트(`.debug/measure-print.mjs`)

**Spec:** [docs/superpowers/specs/2026-10-27-portfolio-card-redesign-design.md](../specs/2026-10-27-portfolio-card-redesign-design.md)

---

## File Structure

| 파일 | 책임 | 변경 |
|---|---|---|
| `css/style.css` | 사이트 전체 스타일 토큰·컴포넌트·인쇄 규칙 | 수정 — `.card-head` 정리, `figure.fig`/`.phase` 신규, 인쇄 규칙 추가 |
| `index.html` | 단일 페이지 콘텐츠 | 수정 — 첫 카드(`#projects` 첫 `details`) 본문 교체 |
| `figure/SurvLLM.png` | SurvLLM 파이프라인 다이어그램 (1063×265) | 신규 추적 — 현재 untracked |

`.debug/`와 `.superpowers/`는 gitignore 대상이므로 커밋에 포함되지 않는다.

---

### Task 1: 제목 줄(.card-head) 정리

뱃지를 오른쪽 끝으로 밀던 `space-between`을 제거해 제목 바로 뒤에 붙이고, `.hint`와의 충돌 회피용 여백을 84px → 54px로 줄인다. 네 카드 전체에 적용된다.

**Files:**
- Modify: `css/style.css:51` (`.card-head`), `css/style.css:54` (`.card-brief`), `css/style.css:99` (모바일 오버라이드)

- [ ] **Step 1: `.card-head` 교체**

`css/style.css:51`의 기존 줄

```css
.card-head { display: flex; justify-content: space-between; align-items: baseline; gap: 10px; flex-wrap: wrap; padding-right: 84px; }
```

을 다음으로 바꾼다.

```css
.card-head { display: flex; align-items: baseline; gap: 8px; flex-wrap: wrap; padding-right: 54px; }
```

- [ ] **Step 2: `.card-brief` 여백 축소**

`css/style.css:54`의 `padding-right: 84px` 를 `padding-right: 54px` 로 바꾼다.

```css
.card-brief { font-size: 13px; color: var(--muted); margin: 4px 0 0; padding-right: 54px; }
```

- [ ] **Step 3: 브라우저로 제목 줄 확인**

`index.html`을 브라우저에서 열고 데스크탑 폭(760px 콘텐츠)에서 네 카드의 제목 줄을 본다.
기대: 뱃지가 제목 텍스트 바로 뒤에 붙고, 네 제목 모두 한 줄에 들어간다. "자세히 ▾" 힌트와 뱃지가 겹치지 않는다.

모바일 오버라이드(`css/style.css:99`)는 `.card-head, .card-brief { padding-right: 0 }`이므로 수정 불필요 — 560px 이하에서 여전히 여백 0이고 `.hint`가 `position: static`으로 아래 줄에 떨어진다.

- [ ] **Step 4: 커밋**

```bash
git add css/style.css
git commit -m "style: 카드 제목 줄 뱃지를 제목 뒤로 붙이고 우측 여백 축소"
```

---

### Task 2: 그림·단계 표시용 CSS 추가

다음 카드에 그림을 넣을 때 HTML 한 덩어리만 추가하면 되도록 범용으로 작성한다.

**Files:**
- Modify: `css/style.css` — `.card-body dd` 규칙(현재 70행) 바로 아래에 삽입

- [ ] **Step 1: `figure.fig` 와 `.phase` 규칙 삽입**

`css/style.css`의 `.card-body dd { ... }` 줄 다음에 아래 블록을 넣는다.

```css
/* 프로젝트 다이어그램 — 본문 최상단 전폭 이미지 + 캡션 */
.card-body .fig { margin: 14px 0 2px; }
.card-body .fig a { display: block; }
.card-body .fig img { width: 100%; display: block; border: 1px solid var(--line); border-radius: 6px; }
.card-body .fig figcaption { font-size: 11.5px; color: var(--faint); margin-top: 6px; text-align: center; }

/* 단계 설명 — 번호칩은 다이어그램 안의 번호와 일치시킨다 */
.phase { display: flex; gap: 8px; margin-top: 7px; }
.phase .pnum {
  flex: 0 0 auto; width: 17px; height: 17px; border-radius: 3px;
  background: var(--ink); color: #fff; font-size: 11px; font-weight: 700;
  display: flex; align-items: center; justify-content: center; margin-top: 3px;
}
.phase p { margin: 0; font-size: 13.5px; color: #333; }
```

- [ ] **Step 2: 커밋**

이 시점에는 적용 대상 HTML이 없어 화면 변화가 없다. 다음 태스크에서 확인한다.

```bash
git add css/style.css
git commit -m "style: 프로젝트 다이어그램·단계 표시용 CSS 추가"
```

---

### Task 3: SurvLLM 카드 본문 교체

`index.html:47-66`의 `<div class="card-body">` 전체를 교체한다. summary(제목·뱃지·한 줄 요약)는 한 줄 요약 문구만 바꾼다.

**Files:**
- Modify: `index.html:44` (card-brief), `index.html:47-66` (card-body)
- Add: `figure/SurvLLM.png` (git 추적 시작)

- [ ] **Step 1: 한 줄 요약 교체**

`index.html:44`

```html
        <p class="card-brief">의무기록지에서 생존분석용 임상 변수를 추출하는 오픈소스 LLM 사후 학습 파이프라인 단독 구축</p>
```

를 다음으로 바꾼다.

```html
        <p class="card-brief">퇴원요약지를 생존분석용 임상 변수로 바꾸는 LLM 사후학습 파이프라인 — 텍스트 규격화 단계 단독 구축</p>
```

- [ ] **Step 2: card-body 전체 교체**

`index.html:47-66` (`<div class="card-body">` 여는 줄부터 닫는 `</div>`까지)를 다음으로 통째 교체한다.

```html
      <div class="card-body">
        <figure class="fig">
          <a href="figure/SurvLLM.png" target="_blank" rel="noopener">
            <img src="figure/SurvLLM.png" alt="퇴원요약지를 규격화 문장으로 바꾼 뒤 생존함수를 추정하는 2단계 파이프라인 도식" width="1063" height="265">
          </a>
          <figcaption>퇴원요약지 → 규격화 문장 → 생존함수</figcaption>
        </figure>
        <dl>
          <dt>개요</dt>
          <dd>비정형 퇴원요약지에서 생존분석용 임상 변수를 추출하는 2단계 파이프라인.
          한 건이 최대 3만 토큰에 달해 수작업 정리가 불가능</dd>
        </dl>
        <div class="phase"><span class="pnum">1</span>
          <p><b>텍스트 규격화</b> — Llama-3.1-8B를 QLoRA로 SFT하고 DPO·PPO로 정렬,
          제각각인 퇴원요약지를 고정된 문장 틀로 변환</p></div>
        <div class="phase"><span class="pnum">2</span>
          <p><b>생존분석</b> — 규격화 문장을 SurvLIFT(Llama-3.2-1B)에 입력하여 생존함수 Ŝ(t) 추정</p></div>
        <dl>
          <dt>기여</dt>
          <dd>Phase 1 전면 담당. 데이터 구축, SFT, 선호도 레이블링(200건 중 130건 직접),
          DPO와 RM+PPO 두 경로 구현을 단독 수행. 규격화 문장을 15개 임상 피쳐로 파싱해 Phase 2에 전달.
          현재 c-index 평가 진행 중</dd>
        </dl>
        <p class="tags"><span>PyTorch</span><span>Transformers</span><span>TRL</span><span>PEFT · QLoRA</span><span>vLLM</span><span>DPO · PPO</span><span>FSDP</span><span>Docker</span><span>wandb</span></p>
        <p class="links"><a href="https://github.com/HollyRiver/SurvLLM" target="_blank" rel="noopener">GitHub</a></p>
      </div>
```

주의: `개요`와 `기여`를 각각 별도 `<dl>`로 두고 그 사이에 `.phase` 블록을 넣는다. `<dl>` 안에 `<div>`를 넣는 것도 HTML5에서 유효하지만, `dt/dd` 사이에 끼어들면 스크린리더의 정의 목록 해석이 깨진다.

두 번째 `<dl>`의 `dt`는 `margin-top: 10px`(`css/style.css:69`)을 그대로 받으므로 간격이 유지된다.

- [ ] **Step 3: 브라우저 육안 확인**

`index.html`을 열고 첫 카드(기본 펼침 상태)를 본다.
기대:
- 본문 맨 위에 다이어그램이 카드 폭 전체로 뜨고, 아래에 회색 캡션 한 줄
- `개요` 아래 한 줄 정의 → 검은 번호칩 ① 텍스트 규격화 → ② 생존분석
- `기여` 항목이 그 아래
- 기술 태그와 GitHub 링크는 그대로
- 그림을 클릭하면 새 탭에 원본이 열림

- [ ] **Step 4: 모바일 폭 확인**

개발자도구 반응형 모드 360px에서 확인.
기대: 그림이 가로 넘침 없이 축소되고, 가로 스크롤바가 생기지 않는다.

- [ ] **Step 5: 커밋**

```bash
git add index.html figure/SurvLLM.png
git commit -m "feat: SurvLLM 카드를 다이어그램 + 개요(Phase 1·2) + 기여 구조로 재구성"
```

---

### Task 4: 인쇄 규칙과 페이지 수 실측

**Files:**
- Modify: `css/style.css` — `@media print` 블록 내부 (현재 105-136행)

- [ ] **Step 1: 그림 분할 방지 규칙 추가**

`@media print` 블록 안, `.card-body dd, .tags, .links { break-inside: avoid; }` 줄 다음에 추가한다.

```css
  .card-body .fig, .phase { break-inside: avoid; }
  .card-body .fig img { border-color: #bbb; }
```

- [ ] **Step 2: 인쇄 페이지 수 실측**

```bash
cd /c/Projects/hollyriver.github.io && node .debug/measure-print.mjs
```

기대: 페이지 수와 꼬리 요소 위치가 출력된다. 항목이 4개→2개로 줄어 분량이 감소했으므로 이전(3페이지)과 같거나 줄어야 한다.

- [ ] **Step 3: footer 원복 가능 여부 판단**

`css/style.css:135`의 `.foot { display: none; }`은 2026-08-22에 footer만 새 페이지로 넘어가는 고아 줄을 막으려 넣은 임시 조치다.

Step 2 실측에서 **마지막 페이지에 footer 두 줄이 들어갈 여유가 있으면** 이 규칙을 되돌린다 — 숨김을 풀고, 원래 의도대로 첫 줄(인쇄 안내 문구)만 숨긴다.

```css
  /* 인쇄 안내 문구는 인쇄본에서 무의미하므로 숨김 */
  .foot p:first-child { display: none; }
```

그리고 `css/style.css:128-134`의 임시 조치 주석 블록을 삭제한다.

**여유가 없으면** 현행 `.foot { display: none; }`을 그대로 두고, 주석의 날짜·근거를 이번 측정 결과로 갱신한다.

- [ ] **Step 4: 실측 재확인**

footer 규칙을 바꿨다면 다시 측정한다.

```bash
node .debug/measure-print.mjs
```

기대: 마지막 페이지에 몇 줄짜리 꼬리만 남은 낭비 페이지가 없다.

- [ ] **Step 5: 커밋**

```bash
git add css/style.css
git commit -m "style: 인쇄 시 다이어그램 분할 방지 및 footer 규칙 정리"
```

---

### Task 5: 스펙 상태 갱신

**Files:**
- Modify: `C:\Projects\HollyRiver\docs\superpowers\specs\2026-10-27-portfolio-card-redesign-design.md:3`
- Modify: `C:\Projects\HollyRiver\docs\superpowers\specs\2026-08-12-portfolio-design.md` — 인쇄 페이지 수 정책 문단

- [ ] **Step 1: 상태 줄 갱신**

```
날짜: 2026-10-27 · 상태: 구현 완료
```

- [ ] **Step 2: 상위 스펙의 인쇄 정책 문단 갱신**

`2026-08-12-portfolio-design.md`의 "현재 임시 조치 (2026-08-22)" 문단을 Task 4 Step 3의 실제 결정(원복했는지 유지했는지)과 측정값으로 갱신한다.

- [ ] **Step 3: 커밋**

```bash
cd /c/Projects/HollyRiver
git add docs/superpowers/specs/
git commit -m "docs: 카드 재구성 구현 완료 및 인쇄 정책 갱신"
```

---

## Self-Review

**Spec coverage**

| 스펙 요구 | 태스크 |
|---|---|
| 범위 — SurvLLM 카드만 재구성 | Task 3 (첫 카드 본문만 교체) |
| 제목 줄 정리를 네 카드 전체 적용 | Task 1 |
| 이미지 → 개요(Phase 1·2) → 기여 구조 | Task 3 Step 2 |
| 명사형 종결·짧은 문장 | Task 3 Step 2 문구 |
| 번호칩이 그림의 ①②와 일치 | Task 3 Step 2 (`pnum` 1·2), Step 3 확인 |
| 신규 CSS를 범용으로 | Task 2 |
| 인쇄 `break-inside: avoid` | Task 4 Step 1 |
| footer 원복 판단 | Task 4 Step 3 |
| 반응형 — 새 탭 원본 링크 | Task 3 Step 2 (`<a>` 래핑), Step 4 확인 |
| 검증 기준 전 항목 | Task 1 Step 3, Task 3 Step 3·4, Task 4 Step 2·4 |

**Placeholder scan** — "적절히", "TBD", "필요시" 류 없음. Task 4 Step 3만 측정 결과에 따라 분기하는데, 두 갈래의 코드와 판단 기준을 모두 적어 두었다.

**Type consistency** — CSS 클래스명이 Task 2 정의(`.fig`, `.phase`, `.pnum`)와 Task 3 사용, Task 4 인쇄 규칙에서 모두 일치한다. Task 2는 `.card-body .fig`로 한정하고 Task 4도 같은 선택자를 쓴다.
