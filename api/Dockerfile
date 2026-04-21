# Stage 1: Builder
FROM python:3.13-slim AS builder
WORKDIR /app
# Hadolint wants apt-get update and install on same line
RUN apt-get update && \
    apt-get install -y --no-install-recommends gcc=4:14.2.0-1 python3-dev=3.13.1-1 && \
    rm -rf /var/lib/apt/lists/*
COPY requirements.txt .
RUN pip install --user --no-cache-dir -r requirements.txt

# Stage 2: Final
FROM python:3.13-slim
# Pin curl version to satisfy DL3008
RUN apt-get update && \
    apt-get install -y --no-install-recommends curl=8.11.1-1 && \
    rm -rf /var/lib/apt/lists/*
RUN groupadd -r appuser && useradd -r -g appuser appuser
WORKDIR /app
COPY --from=builder /root/.local /home/appuser/.local
COPY . .
ENV PATH=/home/appuser/.local/bin:$PATH
USER appuser
HEALTHCHECK --interval=30s --timeout=3s CMD curl -f http://localhost:8000/docs || exit 1
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]