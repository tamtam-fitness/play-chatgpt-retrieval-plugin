
FROM python:3.10 as requirements-stage

WORKDIR /tmp

RUN pip install poetry

COPY ./pyproject.toml ./poetry.lock* /tmp/

RUN pip install --upgrade pip

RUN poetry export -f requirements.txt --output requirements.txt --without-hashes

FROM python:3.10

WORKDIR /code

COPY --from=requirements-stage /tmp/requirements.txt /code/requirements.txt

# ref: https://github.com/Guandaline/chatgpt-retrieval-plugin/commit/f7300c27de86e5481511dcbcd8e33a05b222ecc3#diff-dd2c0eb6ea5cfc6c4bd4eac30934e2d5746747af48fef6da689e85b752f39557
RUN apt-get update
RUN pip install --upgrade pip

RUN apt-get install -y gcc curl
RUN curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh -s -- -y
SHELL ["bash", "-lc"]

RUN pip install --no-cache-dir --upgrade -r /code/requirements.txt

COPY . /code/

# Heroku uses PORT, Azure App Services uses WEBSITES_PORT, Fly.io uses 8080 by default
CMD ["sh", "-c", "uvicorn server.main:app --host 0.0.0.0 --port ${PORT:-${WEBSITES_PORT:-8080}}"]
