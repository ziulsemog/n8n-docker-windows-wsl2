if (-not (Test-Path README.md)) {
@"
# n8n via Docker (Windows + WSL2)

Guia e compose simplificado para subir o n8n localmente com persistência.

## Requisitos
- Windows 10/11 (Build 19041+)
- WSL2 + Ubuntu
- Docker Desktop (engine WSL2)

## Subir
docker compose up -d

## Logs
docker compose logs -f

## Parar
docker compose down

## Acessar
http://localhost:5678
"@ | Out-File -Encoding utf8 README.md
}
