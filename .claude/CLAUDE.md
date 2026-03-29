# CLAUDE.md

Repositorio com agentes IT Valley em pastas individuais.

## Estrutura
- `agents/<agente>/CLAUDE.md`: prompt completo por agente

## Ordem da esteira (core)
- `01-prd-analyst`
- `02-analista-de-tela`
- `03-arquiteto-it-valley-backend`
- `04-arquiteto-it-valley-frontend`
- `05-arquiteto-designer`
- `06-dev-mockado`
- `07-arquiteto-sql-plus-mongodb`
- `08-p-o-product-owner`
- `09-dev-frontend`
- `10-dev-backend`
- `11-qa-unitario`
- `12-qa-integracao`
- `13-qa-tela`
- `14-playwright-e2e`
- `15-guardiao-de-arquitetura`

## Opcionais
- `opc-a-ui-ux-opcional`
- `opc-b-mensageria-opcional`
- `opc-c-engenheiro-de-dados-plus-bi-opcional`

## Deploy (OBRIGATÓRIO — NÃO usar zip deploy)
- **Método**: GitHub Actions (CI/CD via `.github/workflows`)
- **NUNCA** fazer deploy via zip, pacote local ou `az webapp deploy --src-path`
- **Servidor Azure**: `plan-tcc` (App Service Plan)
- **Resource Group**: `rg-webapp`
- **Configurações obrigatórias no App Service**:
  - SCM Basic Auth Publishing: `true`
  - Basic Authentication: `true`
- **Fluxo correto**:
  1. Criar/atualizar workflow em `.github/workflows/`
  2. Usar `actions/checkout` + build + `azure/webapps-deploy` (ou deploy via SCM/Kudu)
  3. Publish profile ou OIDC como credencial (salvar no GitHub Secrets)
  4. Push para o branch trigger → GitHub Actions faz o deploy automaticamente
- **NUNCA gerar arquivo zip para deploy manual**
