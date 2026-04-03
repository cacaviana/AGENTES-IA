# AGENTS.md

## Objetivo
Orquestrar a esteira IT Valley no Codex, garantindo ordem correta, handoff claro entre agentes e paralelismo apenas quando permitido.

## Regra 0 (obrigatoria)
Sempre iniciar perguntando onde esta o PRD.

Pergunta inicial padrao:
`Onde esta o PRD do projeto? Me envie o caminho/arquivo para eu identificar em qual etapa da esteira devemos comecar.`

## Roteamento por etapa

### Se o PRD nao existe
1. Executar `AGENTE 01 - PRD Analyst`.
2. Entregar PRD completo.
3. Confirmar aprovacao do PRD com o usuario.
4. Avancar para `AGENTE 02 - Analista de Tela`.

### Se o PRD ja existe
1. Pular `AGENTE 01`.
2. Executar `AGENTE 02 - Analista de Tela`.

### Depois do Analista de Tela (AGENTE 02)
Executar em paralelo:
1. `AGENTE 02-b - Diagnostico de Infra` ← NOVO (alerta preventivo, NAO bloqueia)
2. `AGENTE 03 - Arquiteto IT Valley Backend`
3. `AGENTE 04 - Arquiteto IT Valley Frontend`
4. `AGENTE 05 - Arquiteto Designer`

Regra: os agentes 02-b, 03, 04, 05 usam como entrada o output do AGENTE 02.
O AGENTE 02-b produz um diagnostico preventivo (nivel 0/1/2/3) que NAO bloqueia a esteira.

### Depois de 03 + 04 + 05
1. Executar `AGENTE 06 - Dev Mockado`.
2. Entregar mockado navegavel com dados falsos realistas.
3. Parar e solicitar validacao do cliente antes de seguir.

## Gate de validacao (obrigatorio)
Nao avancar para etapas seguintes sem aprovacao explicita do usuario no fim do AGENTE 06.

Pergunta padrao de gate:
`O mockado foi aprovado pelo cliente? Se sim, sigo para a proxima fase.`

## Regras especificas de arquitetura (obrigatorias)
- O `AGENTE 03 - Arquiteto IT Valley Backend` e a fonte da verdade da arquitetura backend.
- Todo desenvolvimento backend (AGENTE 10) deve consultar continuamente o output do AGENTE 03.
- API e Service nunca podem violar os contratos definidos pelo AGENTE 03 (camadas, DTO opaco, mapper, factory, repository).
- Se houver conflito entre implementacao e arquitetura, corrigir a implementacao; nao ignorar o AGENTE 03.
- Se uma mudanca arquitetural for necessaria, atualizar primeiro o AGENTE 03 e so depois codificar.

## Agente de conformidade arquitetural
- Usar `AGENTE 15 - Guardiao de Arquitetura` como auditor recorrente.
- Rodar o AGENTE 15 nos checkpoints:
1. Antes de iniciar AGENTE 10 (valida pacote x arquitetura).
2. Durante AGENTE 10 (a cada modulo/stories concluidos).
3. Antes de enviar para QA Unitario/Integracao.
- Nenhum pacote backend segue para QA sem aprovacao do AGENTE 15.

### Depois do Playwright E2E (AGENTE 14) e Guardiao (AGENTE 15)
1. Executar `AGENTE 16 - Arquiteto de Eventos`
2. Entregar relatorio de evolucao arquitetonica para Event-Driven Architecture
3. Relatorio e documento de referencia — NAO modifica codigo

Regra: AGENTE 16 so roda com sistema 100% aprovado (Agentes 11-15 todos aprovados).
Se o Agente 02-b registrou alertas, o AGENTE 16 valida se os sinais se confirmaram.

### Deploy (AGENTE 17)
1. Executar `AGENTE 17 - Engenheiro de Deploy CI/CD`
2. Criar workflows GitHub Actions para backend e frontend
3. Configurar Azure App Service com publish profiles
4. Push para branch trigger = deploy automatico

Regra: NUNCA fazer zip deploy. Sempre GitHub Actions.

## Mapa rapido de entradas e saidas

- AGENTE 01
Entrada: problema bruto
Saida: PRD

- AGENTE 02
Entrada: PRD
Saida: documento de telas

- AGENTE 02-b
Entrada: PRD + documento de telas
Saida: diagnostico preventivo (nivel 0/1/2/3)
Posicao: paralelo com 03/04/05, NAO bloqueia

- AGENTE 03 (paralelo)
Entrada: documento de telas
Saida: arquitetura backend

- AGENTE 04 (paralelo)
Entrada: documento de telas + contratos backend
Saida: arquitetura frontend

- AGENTE 05 (paralelo)
Entrada: documento de telas
Saida: guia visual por tela

- AGENTE 06
Entrada: arquitetura frontend + guia visual
Saida: mockado clicavel + pasta mocks + ambiente mock

- AGENTE 16
Entrada: sistema completo (todos os agentes 11-15 aprovados)
Saida: relatorio de evolucao para Event-Driven Architecture
Posicao: ULTIMO da esteira tecnica, so roda com sistema pronto

- AGENTE 17
Entrada: sistema completo pronto para deploy
Saida: workflows GitHub Actions + configuracao Azure App Service
Posicao: apos AGENTE 16 (ou apos AGENTE 15 se 16 nao for necessario)

## Politica de execucao
- Nao inventar ordem fora desta esteira.
- Sempre informar em qual agente/etapa esta executando.
- Sempre listar artefatos de entrada faltantes antes de comecar um agente.
- Em caso de duvida de etapa, voltar para a pergunta do PRD e identificar ultimo artefato aprovado.
- Em qualquer duvida de arquitetura backend, retornar ao AGENTE 03 e validar com AGENTE 15.
- Monolito e o default — eventos/microservicos so quando a dor real justificar.
- Deploy SEMPRE via GitHub Actions — NUNCA zip deploy.

## Estrutura esperada no repositorio
- `.codex/agents/<agente>/SKILL.md`
- `.claude/agents/<agente>/CLAUDE.md`
- `README.md`
- `AGENTS.md` (este orquestrador)
