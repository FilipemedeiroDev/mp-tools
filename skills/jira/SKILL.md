---
name: "jira"
description: "Cria cards no Jira ou adiciona subtasks em cards existentes, com pontuação."
argument-hint: "[CARD-KEY] sub1 | sub2  —ou—  [PROJECT-KEY] 'Título' [Xpts] [sub1 | sub2]"
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

- Primeiro token contém número (ex: `TP-146`) → **Modo A**
- Primeiro token é só letras (ex: `TP`) → **Modo B**

Extrair:
- `CARD_KEY` ou `PROJECT_KEY`
- `TITULO` (Modo B)
- `PONTOS` — número antes de `pt`, `pts` ou `sp` — opcional
- `SUBTASKS` — lista separada por `|` — opcional

### 2. Carregar credenciais

```bash
python3 -c "
import json
with open('/home/$USER/.claude/settings.json') as f:
    env = json.load(f).get('env', {})
print(env['JIRA_DOMAIN'], env['JIRA_EMAIL'], env['JIRA_API_TOKEN'], env.get('JIRA_STORY_POINTS_FIELD','customfield_10016'))
"
```

### 3. Modo A — Buscar card existente

```bash
python3 -c "
import json, base64, urllib.request
# ... buscar TP-146 e confirmar summary
"
```

Exibir o título do card encontrado antes de criar qualquer coisa.

### 4. Modo B — Criar card novo

```bash
python3 -c "
import json, base64, urllib.request
# POST /rest/api/3/issue com issuetype História ou Story
"
```

### 5. Pontuar (se informado)

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

### 6. Criar subtasks (se informadas)

Para cada item da lista separada por `|`, criar com issuetype `Subtarefa`:

```bash
python3 -c "
import json, base64, urllib.request
# POST /rest/api/3/issue com parent e issuetype Subtarefa
"
```

### 7. Relatório final

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
