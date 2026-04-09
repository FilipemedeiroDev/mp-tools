# mp-tools

Ferramentas internas Multipedidos para Claude Code.

## Instalação

### 1. Adicionar o marketplace

```bash
claude plugin marketplace add FilipemedeiroDev/mp-tools
```

### 2. Instalar o plugin

```bash
claude plugin install mp-tools
```

### 2. Configurar o Jira (primeira vez)

```bash
/mp-tools:jira-setup
```

O comando vai pedir:
- E-mail do Jira
- URL da empresa (ex: `https://suaempresa.atlassian.net`)
- API Token — gere em: https://id.atlassian.com/manage-profile/security/api-tokens

As credenciais ficam salvas localmente em `~/.claude/settings.json` e nunca saem da sua máquina.

---

## Skills disponíveis

### `/mp-tools:jira`

Cria cards, adiciona subtasks e move cards entre colunas no Jira direto pelo Claude.

**Mover card para outra coluna:**
```
/mp-tools:jira TP-146 -> Em Progresso
/mp-tools:jira TP-146 -> Done
/mp-tools:jira TP-146 -> A Fazer
```

**Adicionar subtasks em card existente:**
```
/mp-tools:jira TP-146 | Subtask 1 | Subtask 2 | Subtask 3
/mp-tools:jira TP-146 3pts | Subtask 1 | Subtask 2
```

**Criar card novo:**
```
/mp-tools:jira TP "Título do card" 5pts | Subtask 1 | Subtask 2
/mp-tools:jira TP "Título do card" 5pts
/mp-tools:jira TP "Título do card"
```

---

## Desinstalação

```bash
claude plugin uninstall FilipemedeiroDev/mp-tools
```
