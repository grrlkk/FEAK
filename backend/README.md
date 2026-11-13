# UKTA Backend

- FastAPI

## 채점 모델

KoBERT의 768차원 문장 임베딩과 UKTA 언어 자질 29개를 2층 양방향 GRU(hidden size 256)와 Attention으로 결합합니다.

| 항목 | 내용 |
|:--|:--|
| 가중치 | [`not_topic_model.pth`](apps/cohesion/essay_scoring/model/not_topic_model.pth), 약 11.2MB, 저장소에 포함 |
| 자질 정규화 | [`scaler_notlabel.csv`](apps/cohesion/essay_scoring/features/scaler_notlabel.csv) |
| 추론 코드 | [`essay_scoring.py`](apps/cohesion/essay_scoring/essay_scoring.py), `GRUScoreModuleWithLNUKTAAttention` |
| 학습 시점 | 2025년 9월 27~28일 실험, 가중치 저장일 9월 28일 |
| 모델 출력 | 11개 루브릭, 각 0~3점 |
| 웹 표시 | 주장, 이유와 근거, 다른 입장 고려, 문단 내 조직, 글 전체 조직, 규범, 어휘와 문장: 7개 항목, 총점 100점 |

가중치는 학습 실험 `optuna_results_20250927_134843/best_model.pth`와 모든 텐서가 일치합니다.
KoBERT는 `monologg/kobert`에서 별도로 불러오며, 위 가중치 파일에는 GRU·Attention·출력층이 들어 있습니다.
같은 폴더에 `not_topic_deploy_model.pth`가 있으면 우선 사용하고, 없으면 `not_topic_model.pth`를 사용합니다.

## Setup

Create environment and install dependencies

```bash
pip install -r requirements.txt
```

Run backend API server with 2 workers

```bash
nohup python -m gunicorn -w 2 -k uvicorn.workers.UvicornWorker -b 0.0.0.0:8000 --reload main:app > gunicorn.log 2>&1 &
```

## 피드백 생성

기본 모델은 `gpt-6-luna`이며, `OPENAI_MODEL`로 변경할 수 있습니다.
Luna 호출은 `reasoning_effort="none"`, 기본 temperature 0.2를 사용합니다.
기존 Chat Completions 호출과 JSON 피드백 형식을 유지합니다.
