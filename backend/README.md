# UKTA Backend

- FastAPI

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
