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
