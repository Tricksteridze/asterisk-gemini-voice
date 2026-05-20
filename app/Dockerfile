FROM python:3.11-slim

WORKDIR /app

ENV PYTHONUNBUFFERED=1

RUN apt-get update && apt-get install -y ffmpeg && rm -rf /var/lib/apt/lists/*

RUN pip install --no-cache-dir pexpect asterisk-ami fastapi uvicorn speechrecognition google-genai gTTS

CMD ["python", "main.py"]
