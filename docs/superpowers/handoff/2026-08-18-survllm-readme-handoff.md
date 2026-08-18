# SurvLLM README 정비 — 원격 에이전트 작업 지시문

> 이 문서를 원격 서버(학습 산출물 보관)의 Claude Code에 전달할 것.
> 목적: 포트폴리오(https://hollyriver.github.io/)와 프로필 README에서 링크되는
> `HollyRiver/SurvLLM` 리포의 대외용 README 정비.

## 배경 — 2026-08-18 리포 분리 반영

- 기존 HFRL 리포가 두 개로 분리됨:
  - **`HollyRiver/SurvLLM`** (신규, 대표 리포) — 논문 실험 코드 전체. 루트에 `SFT.py` `DPO.py` `RM.py` `PPO.py` `preference_AIF.py` `vllm_inference.py`와 `config/` `data/` `utils/` `visualization/` `legacy/`, 실행 스크립트 `rlhf.sh` `ppo.sh` `AIF.sh`가 위치.
  - **`HollyRiver/HFRL`** (개발 아카이브) — `SFT/`, `SFT_DPO/` 개발 과정 기록. README는 이미 정비 완료 — **이 리포는 건드리지 말 것.**
- 포트폴리오 대표 카드와 프로필 README의 링크가 모두 github.com/HollyRiver/SurvLLM을 가리키므로, 이 리포의 루트 README가 채용 담당자가 처음 만나는 문서.
- 현재 루트 README는 셋업 노트 + 연구 관찰 메모 성격이라 대외용으로 부적합.
- 핵심 재료: 분리 전 `SurvLLM/CLAUDE.md`(영문, 약 125줄)가 가장 완성도 높은 문서 — 서버 작업 사본에서 위치를 확인해 활용할 것 (리포 분리로 경로가 바뀌었을 수 있음).

## 작업: 루트 `README.md` 전면 재작성 (한국어)

포함할 것:

- 한 줄 소개: MIMIC-IV 퇴원요약지에서 생존분석용 임상 변수를 추출하는 LLM 정렬 파이프라인 (SFT → DPO / RM+PPO, RLHF vs RLAIF 통제 비교)
- Two-Track 연구(LLM 추출 트랙 + SurvLIFT 생존모형 트랙) 중 이 리포는 LLM 트랙임을 명시
- 문제 정의: 최대 16,384 토큰 퇴원요약지 → 고정 포맷 임상 변수 추출, 마스킹된 나이 환각 방지
- 방법 요약: QLoRA(4-bit) SFT → 후보 응답 5개 생성 → 선호도 라벨링(인간 200건 vs Qwen3-30B-A3B RLAIF) → DPO / RM+PPO
- 파이프라인 다이어그램 (CLAUDE.md의 Architecture 절 참고)
- 디렉토리 안내: 루트 스크립트와 `config/` `data/` `utils/` `visualization/` `legacy/`의 역할 — 서버에서 실제 내용을 확인한 뒤 기술
- 핵심 엔지니어링: 4-bit 어댑터 병합 왜곡 문제와 이중 로드(policy/reference) 해법, vLLM 런타임 LoRA, FSDP-QLoRA
- 기술 스택: PyTorch, transformers, TRL, PEFT(QLoRA 4-bit), bitsandbytes, vLLM, FSDP, wandb
- 데이터: MIMIC-IV (Hosp/Patients, Hosp/Admissions, Note/Discharge) — **세부 데이터 내용·예시는 절대 포함 금지 (PhysioNet DUA)**
- **서버에서 확인해 채울 것**: 학습 데이터 규모(subject 수), 대표 학습 곡선/reward margin 그림(`visualization/` 활용),
  wandb에서 공개 가능한 수치(있다면), 현재 진행 단계
- 재현 방법: config 버전 체계(v1.x.x.x + A/H/Am 접미사) 설명, `rlhf.sh`/`ppo.sh`/`AIF.sh` 사용법
- 기존 README의 연구 메모(FSDP-QLoRA 팁, 병합 왜곡 관찰, vLLM 권장, 데이터 품질 관찰 등)는 삭제하지 말고 `NOTES.md`로 이동해 보존

## 문체 규칙 (필수)

- 문장은 명사나 명사형으로 종결 ("~구현.", "~설계.", "~진행 중.")
- 완결 문장이 더 자연스러운 곳은 중립적인 이다체 ("~였다.", "~한다.")
- **~입니다체 금지**

## 주의사항

- 진행중 연구이므로 성능 수치는 확정된 것만 기재하고, 미확정이면 "진행중"으로 명시 (과장 금지)
- MIMIC-IV 데이터 원문·환자 정보가 README, 예시, 그림에 노출되지 않도록 검수
- 포트폴리오 카드 문구와의 일관성: "인간 라벨 200건 중 130건 본인 직접", "실험 설정 45종", "시스템 프롬프트·SFT 레이블 포맷은 공동 연구원 작업"
- `HollyRiver/HFRL` 리포는 이미 정비되어 있으므로 수정 대상 아님
- 완료 후 커밋 메시지: `docs: SurvLLM README 대외용 재구성`
