# 포트폴리오 구축 Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** 국내 데이터/AI 직군 취업용 포트폴리오 — GitHub Pages 사이트(`hollyriver.github.io`) 신규 구축, 프로필 README 개편, 대표 리포 4개 README 정비.

**Architecture:** 순수 HTML/CSS/JS 단일 페이지 사이트(클릭 확장 카드 + 인쇄 시 자동 전체 펼침). 프로필 README는 최소 정보 + 포트폴리오 관문. 리포 README는 프로젝트 상세 증빙 (HFRL은 원격 서버 에이전트에 위임 지시문 작성).

**Tech Stack:** HTML5, CSS(@media print), Vanilla JS(details/summary + beforeprint), gh CLI (repo 생성·Pages·description/topics).

**스펙:** `docs/superpowers/specs/2026-08-12-portfolio-design.md`

**확정 콘텐츠 근거 (2026-08-12 사용자 확인):**
- SurvLLM: 데이터 = MIMIC-IV (Hosp/Patients, Hosp/Admissions, Note/Discharge), 세부 데이터 내용만 비공개하면 표기 가능. Two-Track = LLM 추출 트랙 + SurvLIFT 생존모형 트랙(후자는 공동 작업자 보관). c-index 산출은 됐으나 완결 테이블 없음 → "진행중" 표기. 인간 선호도 라벨 200건 중 130건 본인이 직접. LLM 트랙은 사실상 단독 구축(시스템 프롬프트·이상 레이블 포맷·SFT 레이블링은 타 연구원).
- LLMdetector: 본인 기여 = perplexity 계산·피처 주입 코드, 시각화, LDA-DNN-지표 코드 오류 검토, 버전 관리, 해당 부분 논문 집필, related work 일부 조사. 노트북 수치(이진 97.78% / 5-way 85.98%) = 논문 수치. 인간 텍스트 = Quora, 오염 방지 위해 3년 이전 게시 질문만 (논문 기재 내용).
- TSTF: torch/ 100% 단독, 결과 논문 최종본 반영, 수치는 `ref/original_patchtst_aggregated_result.csv` + `ref/Transfer_Learning_*.pdf`.
- dash: 수업 과제(데이터시각화 기말) 명시 OK. 깨진 index 리다이렉트 수정 등 개선 진행.
- 공통: 이메일 `hcssk2800@gmail.com`으로 교체. KJAS 논문 PDF = `ref/CSAM_LLMC.pdf`.

---

## File Structure

```
C:\Projects\hollyriver.github.io\   (신규 리포)
├── index.html      # 단일 페이지 포트폴리오 (전체 콘텐츠)
├── css/style.css   # 스크린 + @media print
└── js/main.js      # beforeprint/afterprint 카드 자동 펼침

C:\Projects\HollyRiver\README.md    # 프로필 개편 (기존 수정)
C:\Projects\HollyRiver\docs\superpowers\handoff\2026-08-12-hfrl-readme-handoff.md  # 원격 에이전트 지시문

C:\Projects\LLMdetector\README.md   # 신규 작성 (클론 후)
C:\Projects\TSTF\README.md          # 보강 (클론 후)
C:\Projects\dash\README.md          # 신규 작성 + docs/index.html 수정 (클론 후)
```

---

### Task 1: 포트폴리오 사이트 리포 초기화 + index.html

**Files:**
- Create: `C:\Projects\hollyriver.github.io\index.html`

- [ ] **Step 1: 로컬 리포 초기화**

```powershell
New-Item -ItemType Directory -Force C:\Projects\hollyriver.github.io\css, C:\Projects\hollyriver.github.io\js
git -C C:\Projects\hollyriver.github.io init -b main
```

- [ ] **Step 2: index.html 작성** (아래 전체 내용 그대로)

```html
<!DOCTYPE html>
<html lang="ko">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<title>강신성 — Data Scientist Portfolio</title>
<meta name="description" content="통계적 엄밀함으로 LLM을 다루는 데이터 사이언티스트 강신성의 포트폴리오">
<link rel="stylesheet" href="css/style.css">
</head>
<body>
<main class="page">

  <!-- ===== Header ===== -->
  <header class="hero">
    <h1>강신성 <span class="latin">Shinsung Kang</span></h1>
    <p class="tagline">통계적 엄밀함으로 LLM을 다루는 데이터 사이언티스트</p>
    <ul class="contacts">
      <li><a href="mailto:hcssk2800@gmail.com">hcssk2800@gmail.com</a></li>
      <li><a href="https://github.com/HollyRiver" target="_blank" rel="noopener">github.com/HollyRiver</a></li>
      <li><a href="https://scholar.google.com/citations?user=KfeETKAAAAAJ" target="_blank" rel="noopener">Google Scholar</a></li>
      <li><a href="https://velog.io/@hollyriver/posts" target="_blank" rel="noopener">velog</a></li>
    </ul>
  </header>

  <!-- ===== About ===== -->
  <section class="section" id="about">
    <h2>About</h2>
    <p>전북대학교 통계학 학사(2026.08). 학부 과정에서 LLM 관련 연구로 등재지 논문 1편을 공동 1저자로 게재하고,
    IEEE Access 논문의 베이스라인 실험을 담당했습니다. 16K 토큰 규모 의료 텍스트에 대한 LLM 파인튜닝과
    선호도 최적화(RLHF/RLAIF) 파이프라인을 단독 구축하는 등, 통계적 검증과 대규모 실험 엔지니어링을 함께 다룹니다.
    Python·R·SQL 기반의 데이터 분석부터 PyTorch·vLLM 기반의 모델 학습·추론까지 폭넓게 수행합니다.</p>
  </section>

  <!-- ===== Featured Projects ===== -->
  <section class="section" id="projects">
    <h2>Featured Projects</h2>

    <details class="card" open>
      <summary>
        <div class="card-head">
          <span class="card-title">MIMIC-IV 임상 텍스트 LLM 정렬 파이프라인 <span class="repo">SurvLLM</span></span>
          <span class="badge badge-wip">진행중 연구</span>
        </div>
        <p class="card-brief">퇴원요약지에서 생존분석용 임상 변수를 추출하는 Llama-3.1-8B 파인튜닝·선호도 최적화 파이프라인 단독 구축</p>
        <span class="hint" aria-hidden="true"></span>
      </summary>
      <div class="card-body">
        <dl>
          <dt>문제</dt>
          <dd>생존분석의 핵심 입력(활력징후, 입원 검사 수치)이 최대 16,384 토큰의 비정형 퇴원요약지에 묻혀 있습니다.
          마스킹된 환자 나이를 지어내는 등의 환각 없이, 고정된 포맷으로 임상 변수를 추출하는 신뢰 가능한 LLM이 필요했습니다.</dd>
          <dt>접근</dt>
          <dd>MIMIC-IV(Hosp/Patients·Admissions, Note/Discharge) 기반으로 Llama-3.1-8B-Instruct를 QLoRA(4-bit)로
          SFT한 뒤, 후보 응답 5개 생성 → 선호도 라벨링 → DPO와 RM+PPO 두 정렬 경로를 모두 구현했습니다.
          인간 라벨(200건 중 130건 직접 라벨링)과 Qwen3-30B-A3B 심판 라벨(RLAIF)을 동일 조건에서 통제 비교하며,
          실험 설정 45종을 체계적으로 버전 관리했습니다.</dd>
          <dt>엔지니어링</dt>
          <dd>4-bit 모델에 LoRA를 병합하면 SFT 가중치가 왜곡되는 문제를 규명하고, 어댑터를 병합하지 않은 채
          policy/reference로 이중 로드하고 vLLM 런타임 LoRA 장착으로 해결했습니다. FSDP-QLoRA 멀티 GPU 학습,
          RLAIF용 6단계 평가 루브릭과 JSON 순위 검증·재시도 로직을 설계했습니다.</dd>
          <dt>역할 · 현황</dt>
          <dd>Two-Track(LLM 추출 + SurvLIFT 생존모형) 연구 중 LLM 트랙 전체를 담당했습니다
          (시스템 프롬프트·SFT 레이블 포맷은 공동 연구원 작업). 추출 결과를 15개 임상 공변량으로 파싱해
          생존모형 트랙에 연결하며, 현재 성능 평가(c-index) 단계를 진행 중입니다.</dd>
        </dl>
        <p class="tags"><span>PyTorch</span><span>Transformers</span><span>TRL</span><span>PEFT · QLoRA</span><span>vLLM</span><span>DPO · PPO</span><span>FSDP</span><span>Docker</span><span>wandb</span></p>
        <p class="links"><a href="https://github.com/HollyRiver/HFRL/tree/main/SurvLLM" target="_blank" rel="noopener">GitHub</a></p>
      </div>
    </details>

    <details class="card">
      <summary>
        <div class="card-head">
          <span class="card-title">인간/LLM 생성 텍스트 판별 연구 <span class="repo">LLMdetector</span></span>
          <span class="badge badge-pub">KJAS 게재 · 공동 1저자</span>
        </div>
        <p class="card-brief">해석 가능한 통계 피처로 인간 vs LLM 텍스트를 판별 — 이진 정확도 97.8%, 생성 모델 식별 86.0%</p>
        <span class="hint" aria-hidden="true"></span>
      </summary>
      <div class="card-body">
        <dl>
          <dt>문제</dt>
          <dd>LLM 생성 텍스트를 블랙박스 분류기가 아닌 해석 가능한 통계적 근거로 판별하고, 나아가
          GPT·Gemini·Claude·DeepSeek 중 어느 모델이 생성했는지까지 식별하는 문제입니다.</dd>
          <dt>접근</dt>
          <dd>Quora 질문(데이터 오염 방지를 위해 3년 이전 게시글 한정)에 대한 인간·4개 LLM 답변 14,833건의
          코퍼스를 사용해, 문체 통계(어휘 밀도·가독성 지수 등) + Llama-3.1-8B perplexity + 계층적 LDA 토픽 분포
          피처를 결합하고 PyTorch DNN으로 분류했습니다. Ablation 3종으로 피처별 기여도를 정량화했습니다.</dd>
          <dt>성과</dt>
          <dd>이진(human vs LLM) 정확도 <b>97.8%</b>, 5-way 생성 주체 식별 <b>86.0%</b> — 원시 토큰 임베딩
          베이스라인(55.6%) 대비 30%p 이상 향상. 인간 텍스트의 perplexity가 LLM 대비 약 2배 높음을 정량 확인했습니다.</dd>
          <dt>역할</dt>
          <dd>Perplexity 계산·피처 주입 구현, 시각화, LDA·DNN·평가 지표 코드 오류 검토와 버전 관리,
          해당 파트 논문 집필 및 관련 연구 조사를 수행했습니다.</dd>
        </dl>
        <p class="tags"><span>PyTorch</span><span>Transformers</span><span>gensim</span><span>NLTK</span><span>textstat</span><span>scikit-learn</span></p>
        <p class="links"><a href="https://github.com/HollyRiver/LLMdetector" target="_blank" rel="noopener">GitHub</a> <a href="https://doi.org/10.5351/KJAS.2026.39.3.235" target="_blank" rel="noopener">Paper</a></p>
      </div>
    </details>

    <details class="card">
      <summary>
        <div class="card-head">
          <span class="card-title">시계열 전이학습 베이스라인 실험 인프라 <span class="repo">TSTF</span></span>
          <span class="badge badge-pub">IEEE Access 논문 기여</span>
        </div>
        <p class="card-brief">레거시 Keras 실험을 PyTorch로 단독 재구현하고, 데이터셋당 500개 모델 앙상블 실험을 멀티 GPU로 자동화</p>
        <span class="hint" aria-hidden="true"></span>
      </summary>
      <div class="card-body">
        <dl>
          <dt>문제</dt>
          <dd>N-BEATS 전이학습 논문(IEEE Access)의 비교 실험에 필요한 PatchTST 베이스라인이 레거시 Keras 코드로는
          재현·확장이 어려웠습니다. 타겟 데이터셋 7종 × 손실함수 5종 × 모델 100개의 실험 매트릭스를 감당할
          인프라가 필요했습니다.</dd>
          <dt>접근</dt>
          <dd>HuggingFace PatchTST 백본을 M4 데이터로 부트스트랩 사전학습한 뒤 head를 교체(Transformer/LSTM/MLP)해
          파인튜닝하는 전이학습 파이프라인을 PyTorch로 구축하고, median·지수 가중 앙상블로 최종 예측을 산출했습니다.
          21개 실험 태스크를 multiprocessing 큐와 GPU 라운드로빈으로 자동화했습니다 — 결과 존재 시 스킵하는
          재시작 안전 설계로 L40S 2-GPU 활용률 98%를 달성했습니다.</dd>
          <dt>품질 기여</dt>
          <dd>원본 실험 코드의 결함(MASE 손실 오구현, MAPE의 0값 발산, 데이터 분할 분포 불일치)을 발견·문서화하고
          방어 로직을 반영했습니다. 베이스라인 실험 결과는 논문 최종본에 수록되었습니다.</dd>
          <dt>역할</dt>
          <dd><code>torch/</code> 디렉토리 100% 단독 구현 및 결과 집계·보고 (논문 Acknowledgement 기재).</dd>
        </dl>
        <p class="tags"><span>PyTorch</span><span>Transformers · PatchTST</span><span>multiprocessing</span><span>pandas</span></p>
        <p class="links"><a href="https://github.com/HollyRiver/TSTF" target="_blank" rel="noopener">GitHub</a> <a href="https://ieeexplore.ieee.org/abstract/document/11443264" target="_blank" rel="noopener">Paper</a></p>
      </div>
    </details>

    <details class="card">
      <summary>
        <div class="card-head">
          <span class="card-title">NYC 택시 인터랙티브 대시보드 <span class="repo">dash</span></span>
          <span class="badge badge-live">라이브 데모</span>
        </div>
        <p class="card-brief">택시 운행 데이터의 시공간 패턴을 Quarto + Plotly 대시보드로 구축해 GitHub Pages에 배포</p>
        <span class="hint" aria-hidden="true"></span>
      </summary>
      <div class="card-body">
        <dl>
          <dt>문제</dt>
          <dd>수만 건의 NYC 택시 운행 로그에서 "언제, 어디서 택시가 빠르고 느린가"라는 교통 패턴 질문에 답하는
          탐색형 대시보드를 만드는 과제입니다 (데이터시각화 과목 기말 프로젝트).</dd>
          <dt>접근</dt>
          <dd>pandas 메서드 체이닝으로 속력·이동거리·요일·시간대 파생변수를 설계하고 속력을 4분위 구간화했습니다.
          요일×시간대 히트맵 2종과, 승하차 경로를 속력 구간별 색상으로 그린 지도(승객 수를 마커 크기로 인코딩)를
          구성했습니다.</dd>
          <dt>성과</dt>
          <dd>서버 없이 hover·zoom·범례 필터가 동작하는 정적 대시보드를 Quarto로 렌더링하고 GitHub Pages에
          실배포했습니다.</dd>
        </dl>
        <p class="tags"><span>Python</span><span>pandas</span><span>Plotly</span><span>Quarto</span><span>GitHub Pages</span></p>
        <p class="links"><a href="https://github.com/HollyRiver/dash" target="_blank" rel="noopener">GitHub</a> <a href="https://hollyriver.github.io/dash/NYCTaxi.html" target="_blank" rel="noopener">Live Demo</a></p>
      </div>
    </details>
  </section>

  <!-- ===== Publications ===== -->
  <section class="section" id="publications">
    <h2>Publications &amp; Contributions</h2>
    <ol class="pubs">
      <li>Hyungbin Park<sup>†</sup>, <b>Shinsung Kang<sup>†</sup></b>, Kihoon Lee, Gwangsu Kim. (2026).
      Study on comparative and discriminatory methodology of human and machine-generated languages.
      <i>Korean Journal of Applied Statistics</i>, <b>39</b>(3), 235–255.
      <span class="pub-note">† 공동 1저자</span>
      <a href="https://doi.org/10.5351/KJAS.2026.39.3.235" target="_blank" rel="noopener">DOI</a></li>
      <li>Minwoo Lee, Youngmi Lee, Gwangsu Kim. (2026).
      Transfer Learning Based on N-BEATS in Forecasting Univariate Time Series.
      <i>IEEE Access</i>, <b>14</b>, 45191–45212.
      <span class="pub-note">베이스라인 실험 기여 (Acknowledgement)</span>
      <a href="https://ieeexplore.ieee.org/abstract/document/11443264" target="_blank" rel="noopener">Link</a></li>
    </ol>
  </section>

  <!-- ===== Skills ===== -->
  <section class="section" id="skills">
    <h2>Skills</h2>
    <dl class="skills">
      <dt>언어</dt><dd>Python, R, SQL(ANSI·Oracle), Julia, SAS, C#</dd>
      <dt>ML · DL</dt><dd>PyTorch, HuggingFace(Transformers·PEFT·TRL), vLLM, scikit-learn, gensim, NLTK</dd>
      <dt>데이터 · 시각화</dt><dd>pandas, numpy, Plotly, Quarto, matplotlib, seaborn</dd>
      <dt>인프라 · 도구</dt><dd>Linux, Docker, git/GitHub, wandb, 멀티 GPU 학습(FSDP), multiprocessing</dd>
    </dl>
  </section>

  <!-- ===== Education & Certificates ===== -->
  <section class="section" id="education">
    <h2>Education &amp; Certificates</h2>
    <ul class="edu">
      <li>전북대학교 통계학 학사 <span class="muted">(2026.08 졸업)</span></li>
      <li>빅데이터분석기사 (BAE)</li>
      <li>SQLD (SQL Developer)</li>
    </ul>
  </section>

  <footer class="foot">
    <p class="muted">이 페이지는 브라우저 인쇄(PDF 저장) 시 모든 프로젝트가 펼쳐진 형태로 출력됩니다.</p>
    <p class="muted">© 2026 Shinsung Kang · <a href="https://github.com/HollyRiver/hollyriver.github.io">source</a></p>
  </footer>

</main>
<script src="js/main.js"></script>
</body>
</html>
```

- [ ] **Step 3: 커밋**

```powershell
git -C C:\Projects\hollyriver.github.io add index.html; git -C C:\Projects\hollyriver.github.io commit -m "feat: 포트폴리오 페이지 구조 및 콘텐츠 작성"
```

---

### Task 2: 스크린 CSS

**Files:**
- Create: `C:\Projects\hollyriver.github.io\css\style.css`

- [ ] **Step 1: style.css 작성** (스크린 파트 — 전체 내용 그대로)

```css
/* ===== Tokens ===== */
:root {
  --ink: #1c1c1e;
  --muted: #666;
  --faint: #999;
  --accent: #345995;
  --line: #e3e3e6;
  --line-soft: #f0f0f2;
  --bg-soft: #f7f8fa;
}

* { box-sizing: border-box; }
html { scroll-behavior: smooth; }
body {
  margin: 0;
  color: var(--ink);
  background: #fff;
  font-family: "Pretendard", -apple-system, BlinkMacSystemFont, "Segoe UI", "Noto Sans KR", "Malgun Gothic", sans-serif;
  line-height: 1.65;
  font-size: 15px;
}
.page { max-width: 760px; margin: 0 auto; padding: 48px 24px 64px; }
a { color: var(--accent); text-decoration: none; }
a:hover { text-decoration: underline; }
.muted { color: var(--muted); }
code { background: var(--bg-soft); padding: 1px 5px; border-radius: 4px; font-size: 0.9em; }

/* ===== Header ===== */
.hero h1 { font-size: 30px; margin: 0; letter-spacing: -0.5px; }
.hero .latin { font-size: 15px; font-weight: 400; color: var(--faint); margin-left: 4px; }
.tagline { font-size: 15.5px; color: var(--muted); margin: 6px 0 12px; }
.contacts { list-style: none; padding: 0; margin: 0; display: flex; flex-wrap: wrap; gap: 4px 16px; font-size: 13px; }
.hero { border-bottom: 2px solid var(--ink); padding-bottom: 20px; }

/* ===== Sections ===== */
.section { margin-top: 36px; }
.section h2 {
  font-size: 13px; font-weight: 700; text-transform: uppercase;
  letter-spacing: 1.5px; color: var(--accent); margin: 0 0 14px;
}

/* ===== Project cards ===== */
.card { border: 1px solid var(--line); border-radius: 8px; margin-bottom: 10px; background: #fff; }
.card summary {
  cursor: pointer; padding: 14px 16px; list-style: none; position: relative;
  border-radius: 8px; transition: background 0.15s;
}
.card summary::-webkit-details-marker { display: none; }
.card summary:hover { background: var(--bg-soft); }
.card[open] summary { border-bottom: 1px solid var(--line-soft); border-radius: 8px 8px 0 0; }
.card-head { display: flex; justify-content: space-between; align-items: baseline; gap: 10px; flex-wrap: wrap; padding-right: 84px; }
.card-title { font-size: 15.5px; font-weight: 650; }
.card-title .repo { font-size: 12px; font-weight: 500; color: var(--faint); font-family: Consolas, monospace; margin-left: 2px; }
.card-brief { font-size: 13px; color: var(--muted); margin: 4px 0 0; padding-right: 84px; }
.badge { font-size: 11px; font-weight: 600; padding: 2px 9px; border-radius: 10px; white-space: nowrap; }
.badge-wip  { background: #fff7e6; color: #915907; border: 1px solid #f5dfb2; }
.badge-pub  { background: #eef2fb; color: var(--accent); border: 1px solid #d4ddf0; }
.badge-live { background: #e9f7ef; color: #1e7a45; border: 1px solid #c4e8d2; }

/* 확장 affordance: 화살표 + 텍스트 힌트 */
.hint { position: absolute; right: 16px; top: 15px; font-size: 11.5px; color: var(--accent); }
.hint::before { content: "자세히 "; }
.hint::after { content: "▾"; display: inline-block; transition: transform 0.2s; }
.card[open] .hint::before { content: "접기 "; }
.card[open] .hint::after { transform: rotate(180deg); }

.card-body { padding: 4px 16px 14px; }
.card-body dl { margin: 8px 0; }
.card-body dt { font-size: 12px; font-weight: 700; color: var(--accent); margin-top: 10px; }
.card-body dd { margin: 3px 0 0; font-size: 13.5px; color: #333; }
.tags { margin: 12px 0 6px; display: flex; flex-wrap: wrap; gap: 6px; }
.tags span { font-size: 11px; border: 1px solid #d8dce4; color: var(--accent); padding: 1px 9px; border-radius: 10px; }
.links { margin: 6px 0 0; font-size: 13px; }
.links a { margin-right: 12px; font-weight: 600; }

/* ===== Publications ===== */
.pubs { padding-left: 20px; margin: 0; }
.pubs li { font-size: 13.5px; margin-bottom: 12px; color: #333; }
.pubs .pub-note { display: inline-block; font-size: 12px; color: var(--muted); margin: 0 8px 0 4px; }
.pubs sup { font-size: 10px; }

/* ===== Skills ===== */
.skills { display: grid; grid-template-columns: 130px 1fr; gap: 8px 16px; margin: 0; }
.skills dt { font-size: 13px; font-weight: 700; }
.skills dd { margin: 0; font-size: 13.5px; color: #333; }

/* ===== Education ===== */
.edu { list-style: none; padding: 0; margin: 0; font-size: 14px; }
.edu li { margin-bottom: 6px; }

/* ===== Footer ===== */
.foot { margin-top: 48px; border-top: 1px solid var(--line); padding-top: 16px; font-size: 12px; }

/* ===== Mobile ===== */
@media (max-width: 560px) {
  .page { padding: 32px 16px 48px; }
  .skills { grid-template-columns: 1fr; gap: 2px 0; }
  .skills dd { margin-bottom: 8px; }
  .card-head, .card-brief { padding-right: 0; }
  .hint { position: static; display: block; margin-top: 6px; }
}
```

- [ ] **Step 2: 커밋**

```powershell
git -C C:\Projects\hollyriver.github.io add css/style.css; git -C C:\Projects\hollyriver.github.io commit -m "feat: 스크린 스타일 (A×C 절충 디자인, 카드 affordance 포함)"
```

---

### Task 3: 인쇄 CSS + 인쇄 시 자동 펼침 JS

**Files:**
- Modify: `C:\Projects\hollyriver.github.io\css\style.css` (말미에 추가)
- Create: `C:\Projects\hollyriver.github.io\js\main.js`

- [ ] **Step 1: style.css 말미에 print 블록 추가**

```css
/* ===== Print (A4 세로, PDF 제출용) ===== */
@page { size: A4 portrait; margin: 18mm 16mm; }
@media print {
  body { font-size: 11.5px; line-height: 1.55; }
  .page { max-width: none; padding: 0; }
  .hint { display: none; }
  .card { break-inside: avoid; border-color: #bbb; }
  .card summary { cursor: default; }
  .card summary:hover { background: none; }
  .section { margin-top: 22px; }
  .badge-wip, .badge-pub, .badge-live { background: none; border-color: #999; color: #333; }
  .tags span { border-color: #999; color: #333; }
  /* 프로젝트·논문 링크는 URL 병기 */
  .links a::after, .pubs a::after { content: " (" attr(href) ")"; font-weight: 400; font-size: 10px; color: #555; }
  .links a, .pubs a, .contacts a { color: var(--ink); }
  .foot p:first-child { display: none; }
}
```

- [ ] **Step 2: js/main.js 작성** (전체 내용 그대로)

```js
// 인쇄 시 모든 프로젝트 카드를 펼치고, 인쇄 후 원래 상태로 복원한다.
// beforeprint 미지원 환경 대비 matchMedia 리스너 병행.
(function () {
  var saved = null;

  function expandAll() {
    var cards = document.querySelectorAll("details.card");
    saved = Array.prototype.map.call(cards, function (d) { return d.open; });
    cards.forEach(function (d) { d.open = true; });
  }

  function restore() {
    if (saved === null) return;
    var cards = document.querySelectorAll("details.card");
    cards.forEach(function (d, i) { d.open = saved[i]; });
    saved = null;
  }

  window.addEventListener("beforeprint", expandAll);
  window.addEventListener("afterprint", restore);

  if (window.matchMedia) {
    window.matchMedia("print").addEventListener("change", function (e) {
      if (e.matches) expandAll(); else restore();
    });
  }
})();
```

- [ ] **Step 3: 커밋**

```powershell
git -C C:\Projects\hollyriver.github.io add css/style.css js/main.js; git -C C:\Projects\hollyriver.github.io commit -m "feat: 인쇄 스타일 및 인쇄 시 카드 자동 펼침"
```

---

### Task 4: 로컬 검증

- [ ] **Step 1: 로컬 서버 기동**

```powershell
# 백그라운드 실행 (run_in_background)
python -m http.server 8080 --directory C:\Projects\hollyriver.github.io
```

- [ ] **Step 2: 응답 확인**

```powershell
(Invoke-WebRequest http://localhost:8080/ -UseBasicParsing).StatusCode   # 기대: 200
```

- [ ] **Step 3: 수동 검증 체크리스트** (사용자 또는 브라우저 자동화로 확인)

- 카드 클릭 시 펼침/접힘 동작, "자세히 ▾" ↔ "접기 ▴" 힌트 전환
- hover 시 배경 변화
- 첫 카드(SurvLLM)는 기본 펼침 상태
- Ctrl+P 인쇄 미리보기: 전 카드 펼침, 카드 중간 잘림 없음, URL 병기
- 모바일 폭(560px 이하)에서 힌트가 아래로 내려오고 레이아웃 유지

- [ ] **Step 4: 문제 없으면 다음 태스크로, 있으면 수정 후 재검증·커밋**

---

### Task 5: GitHub 리포 생성·푸시·Pages 활성화

- [ ] **Step 1: 리포 생성 및 푸시**

```powershell
gh repo create hollyriver.github.io --public --description "Portfolio of Shinsung Kang — Data Scientist" --source C:\Projects\hollyriver.github.io --push
```

- [ ] **Step 2: Pages 활성화** (user 리포는 자동 활성화되는 경우가 많음 — 먼저 확인 후 필요 시 생성)

```powershell
gh api repos/HollyRiver/hollyriver.github.io/pages 2>$null; if (-not $?) { gh api -X POST repos/HollyRiver/hollyriver.github.io/pages -f "source[branch]=main" -f "source[path]=/" }
```

- [ ] **Step 3: 라이브 확인** (빌드에 1~2분 소요 가능, 실패 시 잠시 후 재시도)

```powershell
(Invoke-WebRequest https://hollyriver.github.io/ -UseBasicParsing).StatusCode   # 기대: 200
```

---

### Task 6: 프로필 README 개편

**Files:**
- Modify: `C:\Projects\HollyRiver\README.md` (전체 교체)

- [ ] **Step 1: README.md 전체를 아래 내용으로 교체**

````markdown
## 강신성 · Shinsung Kang

통계적 엄밀함으로 LLM을 다루는 데이터 사이언티스트

<a href="https://hollyriver.github.io/" target="_blank"><img src="https://img.shields.io/badge/📄_Portfolio-hollyriver.github.io-345995?style=for-the-badge" alt="Portfolio"></a>

전북대학교 통계학 학사 (2026.08). LLM 파인튜닝·선호도 최적화(RLHF/RLAIF)와 통계 모델링을 함께 다룹니다.

### Highlights

* **[SurvLLM](https://github.com/HollyRiver/HFRL/tree/main/SurvLLM)** — MIMIC-IV 퇴원요약지에서 생존분석용 임상 변수를 추출하는 LLM 정렬 파이프라인 (진행중)
* **[LLMdetector](https://github.com/HollyRiver/LLMdetector)** — 인간/LLM 생성 텍스트 판별 연구, *KJAS* **39**(3) 게재 · 공동 1저자 · [Paper](https://doi.org/10.5351/KJAS.2026.39.3.235)
* **[TSTF](https://github.com/HollyRiver/TSTF)** — *IEEE Access* 시계열 전이학습 논문 베이스라인 실험 (Acknowledgement) · [Paper](https://ieeexplore.ieee.org/abstract/document/11443264)
* **[dash](https://github.com/HollyRiver/dash)** — NYC 택시 인터랙티브 대시보드 · [Live Demo](https://hollyriver.github.io/dash/NYCTaxi.html)

<a target="_blank"><img src="https://github-readme-stats-five-delta-79.vercel.app/api?username=HollyRiver&show_icons=true&theme=cobalt" alt="GitHub Stats" width=430></a>

### Contact & Links

<a href="mailto:hcssk2800@gmail.com"><img src="https://img.shields.io/badge/Gmail-hcssk2800-EA4335?style=for-the-badge&logo=gmail&logoColor=white"></a> <a href="https://scholar.google.com/citations?user=KfeETKAAAAAJ&hl=ko" target="_blank"><img src="https://img.shields.io/badge/Google_Scholar-f2f2f2?style=for-the-badge&logo=googlescholar"></a> <a href="https://velog.io/@hollyriver/posts" target="_blank"><img src="https://img.shields.io/badge/Velog-5f5a63?style=for-the-badge&logo=velog&logoColor=black&color=snow"></a> <a href="https://blog.naver.com/hc_ssk2800" target="_blank"><img src="https://img.shields.io/badge/NAVER_blog-1dde30?style=for-the-badge&logo=Naver&logoColor=white"></a>

<sub>🎮 Unity·Aseprite 기반 인디게임도 개발하고 있습니다.</sub>
````

- [ ] **Step 2: 렌더링 확인 후 커밋·푸시**

```powershell
git -C C:\Projects\HollyRiver add README.md; git -C C:\Projects\HollyRiver commit -m "feat: 프로필을 포트폴리오 관문으로 개편 (solved.ac 배지 제거, 이메일 교체)"; git -C C:\Projects\HollyRiver push
```

- [ ] **Step 3: github.com/HollyRiver 에서 렌더링 확인** (배지·링크 동작, solved.ac 부재)

---

### Task 7: LLMdetector README 신규 작성

**Files:**
- Create(교체): `C:\Projects\LLMdetector\README.md`

- [ ] **Step 1: 클론**

```powershell
git clone https://github.com/HollyRiver/LLMdetector C:\Projects\LLMdetector
```

- [ ] **Step 2: README.md 전체 교체** (아래 내용 그대로)

````markdown
# LLMdetector — 인간/LLM 생성 텍스트 판별 연구

인간이 작성한 텍스트와 LLM(GPT·Gemini·Claude·DeepSeek)이 생성한 텍스트를 **해석 가능한 통계적 피처**로
판별하는 연구의 코드베이스입니다.

> **Publication (공동 1저자)**
> Hyungbin Park†, **Shinsung Kang†**, Kihoon Lee, Gwangsu Kim. (2026).
> Study on comparative and discriminatory methodology of human and machine-generated languages.
> *Korean Journal of Applied Statistics*, **39**(3), 235–255. [DOI](https://doi.org/10.5351/KJAS.2026.39.3.235)

## 개요

- **이진 분류**: human vs LLM 판별 — 정확도 **97.8%**
- **다중 분류(5-way)**: human/GPT/Gemini/Claude/DeepSeek 생성 주체 식별 — 정확도 **86.0%** (원시 토큰 베이스라인 55.6%)
- Ablation으로 perplexity 피처의 기여를 정량화 (제외 시 이진 −1.8%p, 다중 −4.4%p)

## 데이터

Quora 질문에 대한 인간 답변과 4개 LLM의 답변, 총 **14,833건**.
LLM 생성 텍스트에 의한 오염을 배제하기 위해 연구 시점 기준 3년 이전에 게시된 질문만 사용했습니다.
5개 주제 카테고리(가상 시나리오, 개인 경험, 철학, 자기계발, 대인관계)로 구성됩니다.

## 방법

1. **문체(stylometric) 피처** — 어휘 밀도, 고유 단어 수, 가독성 지수(Flesch-Kincaid, Gunning Fog, SMOG) 등
2. **Perplexity 피처** — Llama-3.1-8B로 계산 (장문은 stride 512 슬라이딩 윈도우)
3. **계층적 LDA 토픽 피처** — coherence + KneeLocator로 토픽 수 자동 선택 후 서브토픽 확률 분포 사용
4. **분류기** — PyTorch DNN (256→128→64→32, BatchNorm/Dropout, AdamW)

## 리포 구조

```
├── 원본 파일.ipynb / 정리.ipynb / textstat.ipynb   # 데이터 구축·문체 피처·EDA
├── Perplexity/        # Llama-3.1-8B perplexity 계산·분석
├── LDA/               # 계층적 LDA 토픽 모델링
├── model/             # 피처 통합, DNN 학습, ablation, 평가
└── token_feature_model/  # 원시 토큰 임베딩 베이스라인
```

## 담당 파트 (강신성)

Perplexity 계산·피처 주입 구현, 시각화, LDA·DNN·평가 지표 코드 오류 검토 및 버전 관리,
해당 파트 논문 집필, 관련 연구 조사.
````

- [ ] **Step 3: 커밋·푸시**

```powershell
git -C C:\Projects\LLMdetector add README.md; git -C C:\Projects\LLMdetector commit -m "docs: 논문·방법·결과 중심으로 README 전면 작성"; git -C C:\Projects\LLMdetector push
```

- [ ] **Step 4: 리포 description·topics 갱신**

```powershell
gh repo edit HollyRiver/LLMdetector --description "Human vs LLM text detection with interpretable stylometric, perplexity, and LDA features (KJAS 2026, co-first author)" --add-topic nlp --add-topic llm --add-topic text-classification --add-topic pytorch
```

---

### Task 8: TSTF README 보강

**Files:**
- Modify(교체): `C:\Projects\TSTF\README.md`

- [ ] **Step 1: 클론**

```powershell
git clone https://github.com/HollyRiver/TSTF C:\Projects\TSTF
```

- [ ] **Step 2: 기존 README 확인 후 아래 내용으로 교체** (기존의 실행법·데이터 배치 설명 중 유지할 부분이 있으면 "사용법" 절에 병합)

````markdown
# TSTF — 시계열 전이학습 베이스라인 실험 (PyTorch)

IEEE Access 논문 **"Transfer Learning Based on N-BEATS in Forecasting Univariate Time Series"**
(Lee, Lee, Kim, 2026, *IEEE Access* **14**, 45191–45212, [링크](https://ieeexplore.ieee.org/abstract/document/11443264))의
**PatchTST 계열 베이스라인 실험**을 담당한 리포지토리입니다.
저자가 아닌 **Acknowledgement**로 기여했으며, `torch/` 디렉토리는 100% 단독 작업입니다. (`main/`은 원저자 측 레거시 Keras 코드 — 참고용)

## 무엇을 했나

- 레거시 Keras 실험을 **PyTorch + HuggingFace PatchTST**로 재구현
- M4 소스 데이터 부트스트랩 사전학습 → head 교체(Transformer/LSTM/MLP) 파인튜닝 전이학습 파이프라인
- 손실함수 5종(MSE/MAE/MAPE/sMAPE/MASE) × 100개 모델 = **태스크당 500개 모델**의 median·지수 가중 앙상블
- 21개 실험 태스크를 multiprocessing 큐 + GPU 라운드로빈으로 자동화, 결과 존재 시 스킵(재시작 안전) —
  **L40S 2-GPU 활용률 98%**
- 원본 코드의 결함(MASE 손실 오구현, MAPE 0값 발산, 분할 분포 불일치)을 발견·문서화 (`torch/ISSUE.ipynb`)
- 베이스라인 실험 결과는 논문 최종본에 수록

## 데이터

- 소스: M4 (입력 168스텝 → 출력 24스텝)
- 타겟: AIR·coin·ELE·MET·SOL·TEM·WID 7종 (학습 윈도우 252~724개의 소규모 시계열)

## 사용법

```bash
cd torch
bash run_all.sh   # 전체 실험 매트릭스 자동 실행 (GPU 라운드로빈)
```

결과 집계와 앙상블 성능 표는 `torch/result_agg.ipynb` 참고.

## 구조

```
├── data/     # M4 소스 + 7개 타겟 데이터셋
├── main/     # [레거시] 원저자 측 TF/Keras 코드 (참고용)
└── torch/    # [본인 작업] PyTorch 재구현
    ├── run_all.py        # 멀티 GPU 실험 오케스트레이션
    ├── pretraining.py    # M4 사전학습
    ├── trTFTF.py / trTFLSTM.py / trTFMLP.py   # 어댑터 헤드 전이학습
    ├── trPatchTST.py / Scratch*.py            # 비교군
    ├── result_agg.ipynb  # 앙상블 집계
    └── ISSUE.ipynb       # 원본 코드 오류 분석 기록
```
````

- [ ] **Step 3: 커밋·푸시**

```powershell
git -C C:\Projects\TSTF add README.md; git -C C:\Projects\TSTF commit -m "docs: 논문 기여·재구현 범위·실험 인프라 중심으로 README 보강"; git -C C:\Projects\TSTF push
```

- [ ] **Step 4: 리포 description·topics 갱신**

```powershell
gh repo edit HollyRiver/TSTF --description "PyTorch baseline experiments (PatchTST transfer learning) for an IEEE Access paper on N-BEATS-based time series transfer learning" --add-topic time-series --add-topic transfer-learning --add-topic pytorch --add-topic patchtst
```

---

### Task 9: dash 리포 정비 (README + 깨진 리다이렉트 수정 + 정리)

**Files:**
- Modify(교체): `C:\Projects\dash\README.md`
- Modify: `C:\Projects\dash\docs\index.html` (Energy.html → NYCTaxi.html 리다이렉트)
- Delete: `C:\Projects\dash\.ipynb_checkpoints\`, `C:\Projects\dash\Untitled.ipynb`

- [ ] **Step 1: 클론**

```powershell
git clone https://github.com/HollyRiver/dash C:\Projects\dash
```

- [ ] **Step 2: `docs/index.html`의 리다이렉트 대상을 `Energy.html`에서 `NYCTaxi.html`로 수정**
  (파일을 열어 `Energy.html` 문자열을 `NYCTaxi.html`로 치환. meta refresh와 canonical/JS 리다이렉트가 함께 있을 수 있으니 모든 출현을 치환)

- [ ] **Step 3: 수정 검증**

```powershell
Select-String -Path C:\Projects\dash\docs\index.html -Pattern "Energy.html"    # 기대: 출력 없음
Select-String -Path C:\Projects\dash\docs\index.html -Pattern "NYCTaxi.html"   # 기대: 1건 이상
```

- [ ] **Step 4: 잔여물 정리**

```powershell
Remove-Item -Recurse -Force C:\Projects\dash\.ipynb_checkpoints -ErrorAction SilentlyContinue
Remove-Item -Force C:\Projects\dash\Untitled.ipynb -ErrorAction SilentlyContinue
```

- [ ] **Step 5: README.md 전체 교체** (아래 내용 그대로)

````markdown
# NYC Taxi Dashboard

NYC 택시 운행 데이터의 시공간 패턴을 탐색하는 **Quarto + Plotly 인터랙티브 대시보드**입니다.
전북대학교 데이터시각화 과목 기말 프로젝트로 제작했습니다.

**Live Demo → https://hollyriver.github.io/dash/NYCTaxi.html**

## 내용

- **요일 × 시간대 히트맵 2종** — 평균 속력 / 평균 이동거리
- **속력별 운행 경로 지도** — 속력을 4분위 구간화(`pd.qcut`)해 색상으로, 승객 수를 마커 크기로 인코딩
  (`px.line_mapbox` + `px.scatter_mapbox` 합성)
- 서버 없이 hover·zoom·범례 필터가 동작하는 정적 배포 (GitHub Pages)

## 방법

pandas 메서드 체이닝으로 파생변수 설계: log 운행시간, 유클리드 거리, 속력, 요일/시간대.
Quarto dashboard 포맷(`format: dashboard`)으로 렌더링해 `docs/`에 빌드 후 GitHub Pages로 서빙합니다.

## 빌드

```bash
quarto render NYCTaxi.qmd
```

## 구조

```
├── NYCTaxi.qmd     # 메인 대시보드 소스
├── _quarto.yml     # output-dir: docs (GitHub Pages 루트)
├── practice/       # 연습작 (전국 에너지 사용량 choropleth 등)
└── docs/           # 빌드 산출물 = 배포 루트
```

데이터: [guebin/DV2023](https://github.com/guebin/DV2023) 강의 제공 NYCTaxi.csv
````

- [ ] **Step 6: 커밋·푸시**

```powershell
git -C C:\Projects\dash add -A; git -C C:\Projects\dash commit -m "docs: README 전면 작성, 루트 리다이렉트 수정(Energy→NYCTaxi), 잔여물 정리"; git -C C:\Projects\dash push
```

- [ ] **Step 7: 배포 반영 확인** (Pages 재빌드 1~2분 대기 가능)

```powershell
(Invoke-WebRequest https://hollyriver.github.io/dash/ -UseBasicParsing -MaximumRedirection 5).StatusCode   # 기대: 200
```

- [ ] **Step 8: 리포 description·topics 갱신**

```powershell
gh repo edit HollyRiver/dash --description "Interactive NYC taxi dashboard built with Quarto + Plotly, deployed on GitHub Pages" --add-topic data-visualization --add-topic plotly --add-topic quarto --add-topic dashboard
```

---

### Task 10: HFRL — 원격 에이전트 위임 지시문 + description/topics

HFRL/SurvLLM의 README는 학습 산출물(로그·체크포인트·wandb)이 있는 원격 서버의 클로드 에이전트가 작성하는 것이 더 정확하다 (사용자 결정). 여기서는 **지시문 문서**를 만들고, GitHub에서 가능한 정비(description/topics)만 직접 수행한다.

**Files:**
- Create: `C:\Projects\HollyRiver\docs\superpowers\handoff\2026-08-12-hfrl-readme-handoff.md`

- [ ] **Step 1: 지시문 문서 작성** (아래 내용 그대로)

````markdown
# HFRL/SurvLLM README 정비 — 원격 에이전트 작업 지시문

> 이 문서를 원격 서버(학습 산출물 보관)의 Claude Code에 전달하세요.
> 목적: 포트폴리오(https://hollyriver.github.io/)에서 링크되는 HFRL 리포의 대외용 README 정비.

## 배경

- HFRL 리포는 포트폴리오의 최상위 대표 프로젝트 "SurvLLM"의 코드베이스다.
- 현재 루트 README는 7줄(연구 메모 수준), `SurvLLM/README.md`는 셋업 노트 성격이라 채용 담당자가 읽기에 부적합하다.
- `SurvLLM/CLAUDE.md`(125줄, 영문)가 가장 완성도 높은 문서이므로 이를 재료로 활용하라.

## 작업 1: 루트 `README.md` 전면 재작성 (한국어)

포함할 것:
- 한 줄 소개: MIMIC-IV 퇴원요약지에서 생존분석용 임상 변수를 추출하는 LLM 정렬 파이프라인 (SFT → DPO / RM+PPO, RLHF vs RLAIF 통제 비교)
- Two-Track 연구(LLM 추출 트랙 + SurvLIFT 생존모형 트랙) 중 이 리포는 LLM 트랙임을 명시
- 디렉토리 안내: `SurvLLM/`(논문 코드베이스, 핵심) / `SFT/`, `SFT_DPO/`(개발 과정 아카이브) / `RLHF_vis/`(학습 로그 시각화)
- 파이프라인 다이어그램 (CLAUDE.md의 Architecture 절 참고)
- 기술 스택: PyTorch, transformers, TRL, PEFT(QLoRA 4-bit), bitsandbytes, vLLM, FSDP, Docker, wandb
- 데이터: MIMIC-IV (Hosp/Patients, Hosp/Admissions, Note/Discharge) — **세부 데이터 내용·예시는 절대 포함 금지 (PhysioNet DUA)**

## 작업 2: `SurvLLM/README.md` 대외용 재구성 (한국어)

포함할 것:
- 문제 정의: 16K 토큰 퇴원요약지 → 고정 포맷 임상 변수 추출, 나이 환각 방지
- 방법: QLoRA SFT → 후보 5개 생성 → 선호도 라벨링(인간 200건 vs Qwen3-30B-A3B RLAIF) → DPO / RM+PPO
- 핵심 엔지니어링: 4-bit 어댑터 병합 왜곡 문제와 이중 로드 해법, vLLM 런타임 LoRA, FSDP-QLoRA
- **서버에서 확인해 채울 것**: 학습 데이터 규모(subject 수), 대표 학습 곡선/reward margin 그림(RLHF_vis 활용),
  wandb에서 공개 가능한 수치(있다면), 현재 진행 단계
- 재현 방법: config 버전 체계(v1.x.x.x + A/H/Am 접미사) 설명, rlhf.sh/ppo.sh/AIF.sh 사용법
- 기존 연구 메모(관찰 기록)는 `SurvLLM/NOTES.md`로 이동시켜 보존

## 주의사항

- 진행중 연구이므로 성능 수치는 확정된 것만 기재하고, 미확정이면 "진행중"으로 명시 (과장 금지)
- MIMIC-IV 데이터 원문·환자 정보가 README, 예시, 그림에 노출되지 않도록 검수
- 포트폴리오 카드 문구와의 일관성: "인간 라벨 200건 중 130건 본인 직접", "실험 설정 45종", "시스템 프롬프트·SFT 레이블 포맷은 공동 연구원 작업"
- 완료 후 커밋 메시지: `docs: SurvLLM README 대외용 재구성`
````

- [ ] **Step 2: 커밋** (HollyRiver 리포)

```powershell
git -C C:\Projects\HollyRiver add docs/superpowers/handoff/2026-08-12-hfrl-readme-handoff.md; git -C C:\Projects\HollyRiver commit -m "docs: HFRL README 정비 원격 에이전트 지시문 작성"
```

- [ ] **Step 3: HFRL description·topics 갱신** (README와 달리 즉시 가능)

```powershell
gh repo edit HollyRiver/HFRL --description "LLM alignment pipeline (QLoRA SFT, DPO, RM+PPO; RLHF vs RLAIF) for clinical variable extraction from MIMIC-IV discharge summaries" --add-topic llm --add-topic rlhf --add-topic dpo --add-topic qlora --add-topic vllm --add-topic survival-analysis
```

---

### Task 11: 최종 통합 검증

- [ ] **Step 1: 라이브 링크 일괄 확인**

```powershell
@("https://hollyriver.github.io/", "https://hollyriver.github.io/dash/", "https://hollyriver.github.io/dash/NYCTaxi.html", "https://github.com/HollyRiver", "https://github.com/HollyRiver/LLMdetector", "https://github.com/HollyRiver/TSTF", "https://github.com/HollyRiver/HFRL") | ForEach-Object { try { "{0} -> {1}" -f $_, (Invoke-WebRequest $_ -UseBasicParsing -MaximumRedirection 5).StatusCode } catch { "{0} -> FAIL" -f $_ } }
```

기대: 전부 200

- [ ] **Step 2: 스펙 완료 기준 대조**

- 포트폴리오가 hollyriver.github.io에서 열리고 카드 확장이 동작하는가
- 인쇄 미리보기(A4)에서 전체 펼침·페이지 잘림 없음
- 프로필 → 포트폴리오 1클릭 이동
- LLMdetector·TSTF·dash README 정비 완료, HFRL은 지시문 전달 대기 상태

- [ ] **Step 3: 사용자에게 결과 보고** — 라이브 URL, 인쇄 방법(Ctrl+P → PDF 저장), HFRL 지시문 위치·전달 방법 안내

---

## 실행 노트

- Task 1~5는 순차 의존 (사이트). Task 6~10은 사이트 배포 후 어떤 순서든 무방하나, 프로필 README(Task 6)는 포트폴리오 URL이 살아있는지 확인(Task 5) 후 푸시할 것.
- 모든 push는 각 리포의 main 직행 (개인 리포, 기존 관행과 동일).
- 세부 수치가 더 필요하면 `ref/CSAM_LLMC.pdf`(KJAS 논문), `ref/Transfer_Learning_*.pdf`(IEEE 논문), `ref/original_patchtst_aggregated_result.csv` 참조.
