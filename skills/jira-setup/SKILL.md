---
name: "jira-setup"
description: "Configura as credenciais do Jira no ambiente local. Roda uma vez só."
argument-hint: ""
user-invocable: true
disable-model-invocation: true
---

## Outline

Guiar o usuário para configurar as credenciais do Jira em `~/.claude/settings.json`.
Executar os passos em ordem. Ao final, validar se a conexão funciona.

## Steps

### 1. Verificar configuração existente

Ler `~/.claude/settings.json` e verificar se as chaves `JIRA_EMAIL`, `JIRA_API_TOKEN` e `JIRA_DOMAIN` já existem.

- Se existirem: perguntar "Já existe uma configuração do Jira. Deseja sobrescrever? (s/n)"
- Se não existirem: seguir para o passo 2

### 2. Coletar as credenciais

Solicitar ao usuário, uma por vez:

1. **E-mail** — o e-mail usado para acessar o Jira
   > "Qual o seu e-mail do Jira?"

2. **Domínio** — a URL do Jira da empresa
   > "Qual a URL do seu Jira? (ex: https://suaempresa.atlassian.net)"
   > Extrair apenas o subdomínio: `suaempresa`

3. **API Token** — token gerado no Atlassian
   > "Cole seu API Token do Jira."
   > "Não tem um token? Gere em: https://id.atlassian.com/manage-profile/security/api-tokens"

### 3. Salvar em ~/.claude/settings.json

Ler o arquivo atual (ou criar `{}` se não existir) e adicionar/atualizar as chaves dentro de `env`:

```json
{
  "env": {
    "JIRA_EMAIL": "<valor informado>",
    "JIRA_API_TOKEN": "<valor informado>",
    "JIRA_DOMAIN": "<subdomínio extraído>",
    "JIRA_STORY_POINTS_FIELD": "customfield_10016"
  }
}
```

Usar o Read tool para ler o arquivo existente, fazer merge com os novos valores e usar o Write tool para salvar. Preservar todas as outras chaves que já existam no arquivo.

### 4. Validar a conexão

Executar via Bash:

```bash
python3 -c "
import json, base64, urllib.request, urllib.error

with open('/home/$USER/.claude/settings.json') as f:
    env = json.load(f).get('env', {})

domain = env['JIRA_DOMAIN']
email = env['JIRA_EMAIL']
token = env['JIRA_API_TOKEN']

creds = base64.b64encode(f'{email}:{token}'.encode()).decode()
headers = {'Authorization': f'Basic {creds}'}

req = urllib.request.Request(
    f'https://{domain}.atlassian.net/rest/api/3/myself',
    headers=headers
)
try:
    with urllib.request.urlopen(req) as r:
        data = json.load(r)
        print('OK:', data.get('displayName'), '—', data.get('emailAddress'))
except urllib.error.HTTPError as e:
    print('ERRO:', e.code)
"
```

- Se retornar `OK: Nome — email`: exibir mensagem de sucesso
- Se retornar erro 401: avisar que o token está incorreto e pedir para repetir o passo 3
- Se retornar erro de conexão: avisar que o domínio pode estar errado

### 5. Resultado

Exibir:

```
✓ Jira configurado com sucesso!

  Usuário:  <nome retornado pela API>
  Domínio:  <domínio>
  E-mail:   <email>

Agora você pode usar /mp-tools:jira para criar e gerenciar cards.
```
