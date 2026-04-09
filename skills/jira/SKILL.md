---
name: "jira"
description: "Cria cards no Jira, adiciona subtasks em cards existentes, ou move um card para outra coluna."
argument-hint: "[CARD-KEY] sub1 | sub2  —ou—  [PROJECT-KEY] 'Título' [Xpts] [sub1 | sub2]  —ou—  [CARD-KEY] -> 'Status'"
user-invocable: true
disable-model-invocation: true
---

## User Input

```text
$ARGUMENTS
```

## Pré-requisito

Verificar se `JIRA_EMAIL`, `JIRA_API_TOKEN` e `JIRA_DOMAIN` existem em `~/.claude/settings.json`.
Se algum estiver ausente, parar e exibir:

```
⚠️ Jira não configurado. Execute primeiro: /mp-tools:jira-setup
```

## Modos de uso

**Modo C — Mover card para outra coluna**
```
TP-146 -> Em Progresso
TP-146 → Done
TP-146 -> A Fazer
```

**Modo A — Adicionar subtasks em card existente**
```
TP-146 | Subtask 1 | Subtask 2 | Subtask 3
TP-146 3pts | Subtask 1 | Subtask 2
```

**Modo B — Criar card novo**
```
TP "Título do card" 5pts | Subtask 1 | Subtask 2
TP "Título do card" 5pts
TP "Título do card"
```

## Steps

### 1. Detectar o modo

- Input contém `->` ou `→` → **Modo C**
- Primeiro token contém número (ex: `TP-146`) → **Modo A**
- Primeiro token é só letras (ex: `TP`) → **Modo B**

Extrair:
- `CARD_KEY` ou `PROJECT_KEY`
- `STATUS_DESTINO` — texto após `->` ou `→` (Modo C)
- `TITULO` (Modo B)
- `PONTOS` — número antes de `pt`, `pts` ou `sp` — opcional
- `SUBTASKS` — lista separada por `|` — opcional

### 2. Modo C — Mover card

Buscar transições disponíveis para o card:

```bash
python3 -c "
import json, base64, urllib.request
with open('/home/$USER/.claude/settings.json') as f:
    env = json.load(f).get('env', {})
domain = env['JIRA_DOMAIN']
token = base64.b64encode(f\"{env['JIRA_EMAIL']}:{env['JIRA_API_TOKEN']}\".encode()).decode()
req = urllib.request.Request(
    f'https://{domain}.atlassian.net/rest/api/3/issue/$CARD_KEY/transitions',
    headers={'Authorization': f'Basic {token}'}
)
data = json.loads(urllib.request.urlopen(req).read())
for t in data['transitions']:
    print(t['id'], t['name'])
"
```

Encontrar a transição cujo nome corresponda (parcial, case-insensitive) ao `STATUS_DESTINO`.

Se não encontrar, exibir os status disponíveis e parar:
```
✗ Status "X" não encontrado. Transições disponíveis:
  - A Fazer
  - Em Progresso
  - Concluído
```

Se encontrar, executar a transição:

```bash
python3 -c "
import json, base64, urllib.request
with open('/home/$USER/.claude/settings.json') as f:
    env = json.load(f).get('env', {})
domain = env['JIRA_DOMAIN']
token = base64.b64encode(f\"{env['JIRA_EMAIL']}:{env['JIRA_API_TOKEN']}\".encode()).decode()
body = json.dumps({'transition': {'id': '$TRANSITION_ID'}}).encode()
req = urllib.request.Request(
    f'https://{domain}.atlassian.net/rest/api/3/issue/$CARD_KEY/transitions',
    data=body,
    headers={'Authorization': f'Basic {token}', 'Content-Type': 'application/json'},
    method='POST'
)
urllib.request.urlopen(req)
print('ok')
"
```

HTTP 204 = sucesso. Exibir relatório e parar.

### 3. Carregar credenciais

```bash
python3 -c "
import json
with open('/home/$USER/.claude/settings.json') as f:
    env = json.load(f).get('env', {})
print(env['JIRA_DOMAIN'], env['JIRA_EMAIL'], env['JIRA_API_TOKEN'], env.get('JIRA_STORY_POINTS_FIELD','customfield_10016'))
"
```

### 4. Modo A — Buscar card existente

```bash
python3 -c "
import json, base64, urllib.request
# ... buscar TP-146 e confirmar summary
"
```

Exibir o título do card encontrado antes de criar qualquer coisa.

### 5. Modo B — Criar card novo

```bash
python3 -c "
import json, base64, urllib.request
# POST /rest/api/3/issue com issuetype História ou Story
"
```

### 6. Pontuar (se informado)

```bash
python3 -c "
import json, base64, urllib.request
# PUT /rest/api/3/issue/{key} com story points field
"
```

Se retornar erro 400 no campo de pontos, exibir:
```
⚠️ Campo de story points não reconhecido.
   Execute: /mp-tools:jira-setup para reconfigurar.
```

### 7. Criar subtasks (se informadas)

Para cada item da lista separada por `|`, criar com issuetype `Subtarefa`:

```bash
python3 -c "
import json, base64, urllib.request
# POST /rest/api/3/issue com parent e issuetype Subtarefa
"
```

### 8. Relatório final

**Modo B (novo)**:
```
✓ TP-42 criado — "Título do card" (5pts)
  https://<domínio>.atlassian.net/browse/TP-42

  Subtasks:
  ├── TP-43 — Subtask 1
  ├── TP-44 — Subtask 2
  └── TP-45 — Subtask 3
```

**Modo A (existente)**:
```
✓ Subtasks adicionadas em TP-146 — "Título do card"
  https://<domínio>.atlassian.net/browse/TP-146

  ├── TP-43 — Subtask 1
  └── TP-44 — Subtask 2
```

**Modo C (mover)**:
```
✓ TP-146 movido para "Em Progresso"
  https://<domínio>.atlassian.net/browse/TP-146
```
