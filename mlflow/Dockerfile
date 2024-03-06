ARG PYTHON_VERSION=3.11

FROM python:${PYTHON_VERSION}-slim-bullseye

RUN pip install -U pip && pip install \
	mlflow[genai]==2.11.0

WORKDIR /app

COPY config.yaml ./
