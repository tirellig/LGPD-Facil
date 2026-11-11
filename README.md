# LGPDFácil

**LGPD guiada para pequenos negócios sem jurídico, DPO ou equipe especializada.**

A LGPDFácil é uma proposta de plataforma **B2B SaaS** que ajuda micro e pequenas empresas, startups e outros agentes de tratamento de pequeno porte a se organizarem diante da Lei Geral de Proteção de Dados (Lei nº 13.709/2018). Por meio de um teste de enquadramento e de perguntas em linguagem simples, a ferramenta organiza as operações de tratamento de dados, sugere bases legais, gera documentos essenciais, avalia controles mínimos de segurança e fornecedores e monta um plano de ação priorizado por risco.

> Projeto acadêmico desenvolvido na disciplina de **Empreendedorismo e Inovação** (curso de Ciência da Computação). Está em fase de **concepção e validação**.

> ⚠️ **Aviso importante:** a LGPDFácil é uma ferramenta de orientação, organização e execução guiada. **Não é parecer jurídico**, não emite certificação e não garante "conformidade total" com a LGPD.

## Sumário

- [O problema](#o-problema)
- [A proposta](#a-proposta)
- [Protótipo neste repositório](#protótipo-neste-repositório)
- [Como executar](#como-executar)
- [Arquitetura planejada](#arquitetura-planejada)
- [Roadmap](#roadmap)
- [Limites do produto](#limites-do-produto)
- [Referências](#referências)

## O problema

Pequenos negócios tratam dados pessoais todos os dias (cadastro de clientes, folha de pagamento, listas de WhatsApp, câmeras, formulários de site), mas raramente têm jurídico, encarregado (DPO) ou equipe de TI. Pesquisas públicas sobre privacidade em empresas brasileiras mostram que a adequação costuma ser **parcial**: poucas empresas têm alguém formalmente responsável por proteção de dados, e as medidas mais comuns se limitam a contratos e políticas.

A dor principal não é desconhecer a LGPD, e sim a falta de tempo, conhecimento especializado, organização documental, definição de responsáveis, controle de fornecedores e clareza sobre **o que fazer primeiro**.

## A proposta

A ideia não é reproduzir o programa de compliance de uma grande empresa, e sim oferecer uma estrutura **mínima, ágil e demonstrável**, proporcional ao porte e ao risco de cada negócio, com base na **Resolução CD/ANPD nº 2/2022** (alterada pela Resolução CD/ANPD nº 15/2024), que traz regras simplificadas para agentes de tratamento de pequeno porte.

### Três pilares

1. **Governança e transparência:** aviso de privacidade, canal de atendimento ao titular, ponto focal interno de privacidade e políticas essenciais.
2. **Segurança da informação (mínimo viável):** controle de acesso, autenticação em duas etapas, atualizações e antivírus, backup e conscientização contra golpes.
3. **Operações, inventário e contratos:** inventário simplificado das operações de tratamento, mapa de bases legais, cadastro de fornecedores e checklist contratual.

### Jornada do usuário

**Enquadrar → Diagnosticar → Mapear → Proteger → Documentar → Priorizar → Acompanhar**

O diagnóstico usa perguntas do dia a dia, como "Você pede nome, CPF ou telefone de clientes?", "Usa WhatsApp ou e-mail para divulgar promoções?" ou "Há câmeras em áreas abertas ao público?".

### Entregáveis para a empresa

- Relatório de enquadramento como agente de pequeno porte
- Inventário simplificado (registro das operações de tratamento)
- Mapa de bases legais (orientativo)
- Rascunho de aviso de privacidade
- Política simplificada de segurança da informação
- Mapa de fornecedores e checklist contratual
- Plano de ação com prioridade, esforço, responsável e prazo

O painel mostra **progresso e pendências** (itens mapeados, controles implementados, nível de maturidade), nunca um "selo de conformidade".

## Protótipo neste repositório

O arquivo [`index.html`](index.html) é um **protótipo navegável do MVP**, feito em um único arquivo HTML com CSS e JavaScript puros, sem dependências nem build. Ele cobre o fluxo completo:

| Etapa | O que faz |
|---|---|
| Início | Apresentação e avisos sobre os limites da ferramenta |
| Perfil da empresa | Setor, número de colaboradores, canais de atendimento e sistemas usados |
| Enquadramento | Verifica se a empresa pode ser tratada como agente de pequeno porte (porte, receita, grupo econômico e critérios de alto risco) |
| Diagnóstico | Perguntas em linguagem simples sobre os tratamentos de dados |
| Segurança mínima | Checklist dos controles essenciais (sim / parcial / não) |
| Fornecedores | Cadastro de parceiros que recebem dados e situação contratual |
| Plano e documentos | Painel com plano de ação priorizado, inventário e bases legais, fornecedores, documentos gerados e relatório de enquadramento, com exportação via impressão/PDF |

Os dados digitados ficam salvos **apenas no navegador** (`localStorage`). Nada é enviado para servidores. O botão **Carregar exemplo** preenche uma empresa fictícia para demonstração.

## Como executar

Não há instalação. Basta abrir o arquivo no navegador:

```bash
git clone https://github.com/Frank-Cunha/LGPD-Facil.git
cd LGPD-Facil
# abra index.html no navegador, ou sirva a pasta localmente:
python3 -m http.server 8000
# e acesse http://localhost:8000
```

## Arquitetura planejada

O protótipo atual serve para validar o fluxo com usuários. A versão web do MVP será um **monólito modular** com o menor número de peças possível:

- **Next.js + React + TypeScript + Tailwind CSS:** telas, regras de negócio e autorização no servidor, em uma única aplicação
- **Supabase:** PostgreSQL e autenticação, com isolamento entre empresas por Row Level Security
- **Qualidade:** migrations da Supabase CLI, validação com Zod, testes com Vitest e Playwright, CI no GitHub
- **Relatório:** exportado pela impressão do navegador (imprimir ou salvar em PDF), com instruções na própria página

O código é dividido em módulos que seguem a jornada (identidade, enquadramento, diagnóstico, inventário, segurança, fornecedores, plano de ação, documentos, painel e auditoria). Os detalhes estão no [CLAUDE.md](CLAUDE.md).

Requisitos desde o início: HTTPS, segredos fora do repositório, isolamento entre empresas no servidor e no banco, controle de acesso por papel, trilha de auditoria, backups com teste de restauração, minimização de dados e boas práticas do OWASP.

## Roadmap

1. **Descoberta:** entrevistas com empresas para validar dores e linguagem.
2. **MVP concierge:** diagnóstico e plano de ação feitos de forma assistida com empresas piloto.
3. **Protótipo navegável:** enquadramento, diagnóstico e painel (este repositório).
4. **MVP web:** cadastro e login, enquadramento, diagnóstico versionado, motor de regras, plano de ação e relatório para impressão.
5. **Modelo recorrente:** assinatura com revisões periódicas e alertas.
6. **Expansão:** canal do titular integrado, trilhas de treinamento, biblioteca contratual, integrações e apoio especializado.

## Limites do produto

- Não promete certificação, imunidade a sanções nem "100% de conformidade".
- As bases legais sugeridas são **orientativas** e não substituem análise jurídica.
- Casos de alto risco, dados sensíveis em larga escala, dados de crianças e adolescentes, decisões automatizadas relevantes e incidentes graves devem ser encaminhados a um profissional especializado.
- Documentos gerados são **rascunhos** e devem ser revisados antes de publicados.

## Referências

- [Lei nº 13.709/2018 (LGPD)](https://www.planalto.gov.br/ccivil_03/_ato2015-2018/2018/lei/l13709.htm)
- Resolução CD/ANPD nº 2/2022, alterada pela Resolução CD/ANPD nº 15/2024 (disponível no [site da ANPD](https://www.gov.br/anpd/pt-br))
- ANPD, *Guia orientativo de segurança da informação para agentes de tratamento de pequeno porte*
- ANPD, modelo de registro simplificado de operações de tratamento para agentes de pequeno porte
- Cetic.br/NIC.br, *Pesquisa sobre Privacidade e Proteção de Dados Pessoais*
- OWASP, *Application Security Verification Standard*
