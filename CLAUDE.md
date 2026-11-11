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

## Stack planejada para o MVP web

Ainda não implementada. Quando começar, siga esta direção, salvo decisão diferente do grupo:

- Monólito modular (não usar microsserviços no MVP).
- Frontend: Next.js + React + TypeScript + Tailwind CSS, web responsiva (sem app nativo no MVP).
- Backend: Python + FastAPI, com regras de negócio e autorização no servidor.
- Banco: PostgreSQL com serviço gerenciado de autenticação; migrations com SQLAlchemy/Alembic.
- Testes: Pytest e Playwright; CI no GitHub.
- Entidades mínimas: `empresas` (tenant), `usuarios` (papel administrador/colaborador), `perguntas` (versionadas), `diagnosticos`, `respostas`, `acoes`, `auditoria`.
- Fora do MVP: pagamentos automáticos, app móvel e IA generativa.

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
