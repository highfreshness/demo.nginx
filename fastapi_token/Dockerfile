FROM python:3.10.13-bullseye

WORKDIR /app

RUN pip install "fastapi[all]" "passlib[bcrypt]" "python-jose[cryptography]"

COPY main.py ./

CMD ["uvicorn", "main:app", "--reload", "--host", "0.0.0.0", "--log-level", "debug"]

