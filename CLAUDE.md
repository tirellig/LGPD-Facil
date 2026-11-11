# CLAUDE.md

Orientações para o Claude Code (e outros agentes de IA) ao trabalhar neste repositório. Para a visão geral do projeto, veja o [README](README.md).

## Sobre o projeto

**LGPDFácil** é uma proposta de plataforma **B2B SaaS** de adequação simplificada à LGPD para micro e pequenas empresas, startups e outros agentes de tratamento de pequeno porte. É um projeto acadêmico da disciplina de Empreendedorismo e Inovação, hoje em fase de **concepção e validação**.

- Proposta de valor: "LGPD guiada para pequenos negócios sem jurídico, DPO ou equipe especializada."
- Público: dono, administrador ou responsável por operações/financeiro de uma PME, sem formação jurídica ou técnica.
- Referencial normativo: Resolução CD/ANPD nº 2/2022 (agentes de pequeno porte), alterada pela Resolução CD/ANPD nº 15/2024, e o Guia de segurança da ANPD para agentes de pequeno porte.
- Jornada: **Enquadrar → Diagnosticar → Mapear → Proteger → Documentar → Priorizar → Acompanhar**.

## Idioma

Todo o conteúdo voltado ao usuário, documentação, comentários e mensagens de commit são escritos em **português do Brasil**. Use linguagem simples e direta, sem juridiquês, porque o público não é especialista.

## Estado atual do código

O repositório contém um **protótipo navegável** em um único arquivo:

- `index.html`: HTML, CSS e JavaScript puros, sem dependências, sem build e sem backend. Abra direto no navegador ou sirva com `python3 -m http.server`.

Estrutura interna do `index.html`:

| Bloco | Conteúdo |
|---|---|
| `<style>` | Tema em variáveis CSS no `:root` (`--brand`, cores de prioridade `--alta`, `--media`, `--cont`, etc.) e regras de impressão (`.no-print`, `.print-only`) |
| Constantes de conteúdo | `STEPS` (etapas do fluxo), `CANAIS`, `CRIT_ESPECIFICOS` (critérios de alto risco), `DIAG` (perguntas do diagnóstico), `SEG` (checklist de segurança), `TIPOS_FORN` |
| Estado | Objeto `S`, criado por `blank()` e persistido em `localStorage` na chave `lgpdfacil-mvp-v1` via `load()` / `save()` |
| Regras de negócio | `enquadramento()` (pequeno porte ou não), `inventario()` (operações e bases legais orientativas), `tarefas()` (plano de ação ordenado por prioridade `alta` → `media` → `cont`), `maturidade()` (percentual e nível) |
| Documentos | `docAviso()` (aviso de privacidade) e `docSeguranca()` (política simplificada de segurança), gerados como texto a partir do estado |
| Renderização | Objeto `V` com uma função de view por etapa (chave = `id` em `STEPS`), `render()`, `renderSteps()`, `go(i)`; eventos tratados por delegação com atributos `data-*` (`data-go`, `data-tab`, `data-in`, `data-copy`, `data-star`...) |
| Demonstração | `demo()` carrega uma empresa fictícia |

Convenções ao editar o protótipo:

- Mantenha o arquivo único e sem dependências externas, a menos que a mudança seja explicitamente uma migração para a stack planejada.
- Todo texto vindo do usuário deve passar por `esc()` antes de entrar em `innerHTML`.
- Para adicionar uma pergunta, um controle de segurança ou uma etapa, edite as constantes (`DIAG`, `SEG`, `STEPS`) e as regras correspondentes em `tarefas()`, `inventario()` e `maturidade()`, além da view em `V`.
- Se o formato do estado `S` mudar de forma incompatível, troque a versão da chave do `localStorage` (por exemplo, `lgpdfacil-mvp-v2`).
- Não há testes automatizados no protótipo. Valide abrindo no navegador: percorra o fluxo, use **Carregar exemplo**, confira o painel e a impressão/PDF, e teste em largura de celular.

## Stack do MVP web

Decisão do grupo: **somente Next.js e Supabase**, para ter o menor número de peças possível, uma única linguagem (TypeScript) e um único deploy. Ainda não implementada. Quando começar, siga esta direção:

- **Next.js (App Router) + React + TypeScript + Tailwind CSS**, web responsiva (sem app nativo no MVP). Regras de negócio e autorização rodam no servidor, em Server Components, Server Actions e Route Handlers. Nada de regra de negócio só no navegador.
- **Supabase** para PostgreSQL e autenticação. Isolamento entre empresas com **Row Level Security (RLS)** em todas as tabelas que têm `empresa_id`, além da checagem no servidor. Projeto na região de São Paulo.
- Migrations em SQL com a Supabase CLI (`supabase/migrations/`) e tipos TypeScript gerados a partir do banco.
- Validação de entrada com Zod em toda Server Action e Route Handler.
- Testes: Vitest para as regras dos módulos e Playwright para o fluxo principal. CI no GitHub Actions.
- Hospedagem do Next.js na Vercel. O plano gratuito da Vercel é para uso não comercial: ao cobrar clientes, passe para um plano pago.
- **Sem geração de PDF no servidor.** O relatório é exportado pela impressão do navegador (CSS `@media print`), e a página mostra um aviso visível explicando como imprimir ou salvar em PDF no computador e no celular.
- Fora do MVP: pagamentos automáticos, app móvel e IA generativa.

### Arquitetura modular (obrigatória)

Monólito modular: um único app Next.js dividido em módulos por etapa da jornada. Não usar microsserviços.

```
src/
  app/                 # rotas e telas do Next.js; só compõem módulos, sem regra de negócio
  modules/
    identidade/        # empresas (tenant), usuários, papéis administrador/colaborador
    enquadramento/     # teste de agente de pequeno porte
    diagnostico/       # perguntas versionadas e respostas
    inventario/        # operações de tratamento e bases legais orientativas
    seguranca/         # checklist de segurança mínima
    fornecedores/      # fornecedores e situação contratual
    plano-acao/        # ações com prioridade, responsável, prazo e status
    documentos/        # rascunhos de aviso de privacidade e política de segurança
    painel/            # progresso, maturidade e versão para impressão (só leitura)
    auditoria/         # trilha de eventos (transversal)
  shared/              # cliente Supabase, componentes de UI, utilitários
content/               # perguntas, regras e modelos de documento versionados (revisados pelo jurídico)
supabase/migrations/
```

Cada módulo segue o mesmo formato: `index.ts` (interface pública), `regras.ts` (funções puras, sem banco e sem rede), `dados.ts` (acesso ao Supabase), `acoes.ts` (Server Actions), `ui/` (componentes) e testes `*.test.ts` ao lado.

Regras entre módulos:

- Um módulo só importa outro pelo `index.ts`. Nunca importe arquivos internos de outro módulo.
- Cada módulo é dono das suas tabelas. Para ler dados de outro módulo, use a função pública dele.
- `regras.ts` não acessa banco, rede nem `Date.now()` direto: recebe tudo por parâmetro, para ser testável. As regras do protótipo (`enquadramento()`, `inventario()`, `tarefas()`, `maturidade()`) são o ponto de partida.
- Toda ação ou recomendação gerada guarda qual regra a originou e a versão do conteúdo, para explicar o porquê ao usuário.
- Texto jurídico (perguntas, regras, modelos de documento) fica em `content/`, com versão, e passa por revisão de alguém de Direito antes de ir para produção.

Entidades mínimas: `empresas` (tenant), `usuarios` (papel), `perfis`, `enquadramentos`, `perguntas` (versionadas), `diagnosticos`, `respostas`, `operacoes` (inventário), `controles_seguranca`, `fornecedores`, `acoes` (com `regra_id`, responsável, prazo e status), `documentos` (versão e status de rascunho) e `auditoria`.

## Regras de produto (obrigatórias em código, textos e documentação)

- **Nunca** dizer ou sugerir que a empresa está "100% em conformidade", "certificada" ou "imune a sanções". Use termos como itens mapeados, controles implementados, pendências e nível de maturidade.
- Progresso no painel significa **tarefas executadas**, não adequação jurídica comprovada.
- Bases legais sugeridas são **orientativas** e não são parecer jurídico. Deixe isso visível na interface.
- Documentos gerados são **rascunhos** e devem trazer aviso para revisão antes da publicação.
- Encaminhar para especialista: alto risco, dados sensíveis em larga escala, dados de crianças e adolescentes, decisões automatizadas relevantes, incidentes graves e operações complexas.
- Nem toda PME é agente de pequeno porte: o fluxo sempre começa pelo teste de enquadramento.
- Segurança é apresentada como "mínimo viável", nunca como "segurança completa".

## Privacidade e segurança do próprio produto

A plataforma lida com informações sensíveis sobre as empresas clientes, então precisa praticar o que recomenda:

- Minimização de dados: colete só o necessário.
- Nada de segredos, chaves de API ou credenciais no repositório. Use variáveis de ambiente.
- Isolamento entre empresas verificado no backend em toda operação.
- Validação de entrada, rate limiting e trilha de auditoria sem expor segredos.

## Repositório público

Este repositório é **público**. Não adicione:

- Dados pessoais de integrantes do grupo, entrevistados ou empresas reais (nomes, e-mails, telefones, documentos).
- Transcrições, respostas ou anotações de entrevistas e pesquisas de validação.
- Hipóteses internas de preço, projeções financeiras, custos ou estratégia comercial.
- Dados reais de clientes em exemplos ou testes: use sempre dados fictícios.

## Histórico de alterações

**Regra para agentes de IA e para o grupo:** toda alteração de código, conteúdo ou configuração deve adicionar uma entrada nesta seção **no mesmo commit**, sem esperar alguém pedir. Entradas novas ficam no topo. Use o formato:

`- AAAA-MM-DD · resumo curto do que mudou e por quê (arquivos ou módulos afetados)`

Se a mudança alterar estrutura, stack ou convenções, atualize também as seções acima deste arquivo. Não registre aqui dados pessoais, preços ou informações internas.

- 2026-11-11 · Stack do MVP reduzida para Next.js + Supabase, arquitetura modular definida, PDF automático substituído pela impressão do navegador e criado este histórico (`CLAUDE.md`, `README.md`). Protótipo ganhou instruções visíveis de como imprimir ou salvar em PDF (`index.html`).
