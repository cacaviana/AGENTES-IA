# CLAUDE.md

Repositorio com agentes IT Valley em pastas individuais.

## Estrutura
- `agents/<agente>/CLAUDE.md`: prompt completo por agente

## Ordem da esteira (core)

### DESCOBERTA
- `01-prd-analyst`
- `02-analista-de-tela`

### ARQUITETURA (paralelo)
- `02-b-diagnostico-infra` ← Alerta preventivo (mensageria/microservicos/eventos). NAO bloqueia.
- `03-arquiteto-it-valley-backend`
- `04-arquiteto-it-valley-frontend`
- `05-arquiteto-designer`

### VALIDACAO
- `06-dev-mockado` → Gate de aprovacao do cliente

### DADOS E PLANEJAMENTO
- `07-arquiteto-sql-plus-mongodb`
- `08-p-o-product-owner`

### DESENVOLVIMENTO (paralelo)
- `09-dev-frontend`
- `10-dev-backend`
- `15-guardiao-de-arquitetura` (recorrente)

### QUALITY ASSURANCE (sequencial)
- `11-qa-unitario`
- `12-qa-integracao`
- `13-qa-tela`
- `14-playwright-e2e`

### EVOLUCAO
- `16-arquiteto-eventos` ← Relatorio final de evolucao para Event-Driven Architecture. So roda com sistema 100% aprovado.

### DEPLOY
- `17-deploy-cicd` ← Deploy via GitHub Actions para Azure App Service. NUNCA zip deploy.

## Opcionais
- `opc-a-ui-ux-opcional`
- `opc-b-mensageria-opcional`
- `opc-c-engenheiro-de-dados-plus-bi-opcional`

## Regras fundamentais
1. Monolito e o default — so adicionar eventos/microservicos quando a dor real justificar
2. Eventos sao fatos no passado (PedidoCriado) — producers nao conhecem consumers
3. Sistema nasce monolito, evolui para eventos — nunca o contrario
4. Deploy SEMPRE via GitHub Actions — NUNCA zip deploy ou pacote local
5. Azure App Service Plan: `plan-tcc`, Resource Group: `rg-webapps`
6. SCM Basic Auth Publishing e Basic Authentication: sempre `true`

## Deploy (OBRIGATORIO — NAO usar zip deploy)
- **Metodo**: GitHub Actions (CI/CD via `.github/workflows`)
- **NUNCA** fazer deploy via zip, pacote local ou `az webapp deploy --src-path`
- **Servidor Azure**: `plan-tcc` (App Service Plan)
- **Resource Group**: `rg-webapps`
- **Configuracoes obrigatorias no App Service**:
  - SCM Basic Auth Publishing: `true`
  - Basic Authentication: `true`
- **Fluxo correto**:
  1. Criar/atualizar workflow em `.github/workflows/`
  2. Usar `actions/checkout` + build + `azure/webapps-deploy` (ou deploy via SCM/Kudu)
  3. Publish profile ou OIDC como credencial (salvar no GitHub Secrets)
  4. Push para o branch trigger → GitHub Actions faz o deploy automaticamente
- **NUNCA gerar arquivo zip para deploy manual**
