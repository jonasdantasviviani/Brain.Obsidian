---
tipo: padrao
titulo: Script de setup cria o .env a partir do .env.example
projeto: [BTech.NFe.Api, BTech.Web]
stack: [powershell, bash, docker]
tags: [tipo/padrao, env, setup-local, onboarding, seguranca]
palavras-chave: [env, env.example, setup local, clone novo, maquina nova, onboarding, gitignore, segredo, copy-item, cp, script de setup]
origem: claude-code
criado: 2026-09-18
atualizado: 2026-09-18
confianca: alta
---

# Script de setup cria o .env a partir do .env.example

## Resumo
Arquivo de segredo não entra no git — mas o script de setup **cria** ele do `.env.example` na
primeira execução, em vez de abortar dizendo que não existe.

## Contexto
`.env` no `.gitignore` é regra ([[02-limpar-secrets-do-git]]). A consequência é que todo clone novo
nasce sem ele, e qualquer script que exija o arquivo quebra na máquina nova — ver
[[env-nunca-foi-commitado-clone-novo-nao-roda]]. As duas exigências convivem: o repositório guarda
o **molde** (`.env.example`, versionado, com os campos sensíveis vazios), e o script materializa a
cópia local.

## Detalhe

### A regra
Todo script que **lê** o `.env` também sabe **criá-lo**. Falhar só quando nem o `.env.example`
existe — aí é repositório quebrado, não setup incompleto.

PowerShell:

```powershell
if (-not (Test-Path $EnvFile)){
    $exemplo = Join-Path $PSScriptRoot '..\.env.example'
    if (-not (Test-Path $exemplo)){
        Write-Error "Env file not found: $EnvFile (e não achei o .env.example para copiar)"
        exit 1
    }
    Copy-Item $exemplo $EnvFile
    Write-Host "Criado $EnvFile a partir do .env.example." -ForegroundColor Yellow
    Write-Host '  Preencha FOCUS_NFE_TOKEN e SMTP_* se for emitir nota ou enviar XML.' -ForegroundColor Yellow
}
```

Bash:

```bash
if [[ ! -f "$ENV_FILE" ]]; then
  EXEMPLO="$SCRIPT_DIR/../.env.example"
  [[ -f "$EXEMPLO" ]] || { echo "Env file not found: $ENV_FILE (e não achei o .env.example para copiar)" >&2; exit 1; }
  cp "$EXEMPLO" "$ENV_FILE"
  echo "Criado $ENV_FILE a partir do .env.example."
fi
```

### O que o .env.example precisa ter
Valores de dev que **funcionam sem edição** (senha do SA local, nome do banco, portas) e campos
sensíveis **vazios** (`FOCUS_NFE_TOKEN=`, `SMTP_SENHA=`). Assim a cópia crua já sobe a stack, e o
que falta está explícito. A mensagem do script diz qual campo preencher e para quê — sem isso o
dev descobre pelo erro em runtime.

### Onde colocar
Em **todos** os pontos de entrada, não só no principal. No [[BTech.NFe.Api]] são quatro:
`setup-local.ps1`, `setup-local.sh`, `run-local-api.ps1`, `run-local-api.sh`. Um ponto de entrada
esquecido reabre o bug para quem usa aquele caminho.

### Line ending
Marque o molde como LF no `.gitattributes` (`.env.example text eol=lf`). Sem isso o clone Windows
gera um `.env` CRLF e o loader do `.sh` leva `\r` para dentro da connection string.

## Relacionado
- [[env-nunca-foi-commitado-clone-novo-nao-roda]]
- [[02-limpar-secrets-do-git]]
- [[01-esconder-api-keys]]
- [[BTech.NFe.Api]]
