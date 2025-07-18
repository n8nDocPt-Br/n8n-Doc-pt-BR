---
sidebar_position: 1
title: Introdução ao n8n
description: Guia completo para começar com n8n
keywords: [n8n, automação, workflow, introdução, guia, open source]
---


## <ion-icon name="play-circle-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Introdução ao n8n

Esta é a documentação em português do n8n, plataforma open-source que permite construir automações inteligentes e workflows avançados, com flexibilidade e controle total.

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Origem e evolução

O n8n foi idealizado em **2019** por **[Jan Oberhauser](https://github.com/janober)**, que buscava uma solução capaz de conectar apps e APIs sem a complexidade dos scripts convencionais. Com foco em código aberto e modelo "fair‑code", o projeto cresceu rapidamente e, em **abril de 2025**, atingiu a marca de **[100.000 estrelas no GitHub](https://github.com/n8n-io/n8n)**. Desde então, a comunidade só tem se fortalecido.

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> O que é o n8n?

O n8n (pronuncia-se "n‑eight‑n") é uma plataforma que oferece:

- **Editor visual de workflows** com mais de **400 integrações** nativas
- **Nós de código** em JavaScript/Python para lógica específica
- **Plugins e triggers avançados**, como webhooks, automações agendadas e ações via IA
- **Autonomia total**: pode ser executado localmente ou na nuvem, sem dependência de terceiros
- **Gratuito e open-source**, sob licença "fair‑code", que permite uso livre e comunidade ativa

### Conceitos fundamentais

Para entender completamente o n8n, é essencial conhecer os conceitos fundamentais que formam a base da plataforma. **[Consulte nossa seção dedicada de Conceitos Fundamentais](./primeiros-passos/conceitos-fundamentais)** para uma explicação completa de:

- **Workflows** - Sequências visuais de etapas automatizadas
- **Nodes** - Blocos de construção dos workflows
- **Triggers** - Disparadores que iniciam workflows
- **Connections** - Ligações entre nodes
- **Execution** - Processo de execução de workflows
- **Data Mapping** - Mapeamento de dados entre nodes
- **Error Handling** - Tratamento de erros

Esses conceitos são a base para criar automações eficientes no n8n.

---

## <ion-icon name="grid-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> O que você vai aprender aqui

Esta introdução está estruturada para guiar você de forma progressiva:

1. **Conceitos fundamentais** – entenda automação e interface do n8n
2. **Configuração e instalação** – passo a passo para ter o ambiente pronto
3. **Construção do primeiro workflow** – um caso real, simples e funcional
4. **Exploração de nós e integrações** – conheça os principais recursos
5. **Expansão prática** – adicione lógica, tratamento de erros e deploy

Ao final, você terá autonomia para criar sua própria automação, seja um alerta por Slack ou integração entre APIs.

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Por que escolher o n8n

### Para  pessoas desenvolvedoras

- **API REST robusta**, triggers avançados e execução de código customizado
- **Webhooks nativos** e integração com sistemas existentes
- **Versionamento** e deploy automatizado de workflows

### Para equipes e empresas

- **Redução de custos operacionais** e infraestrutura flexível
- **Integração entre sistemas legados** e novas tecnologias
- **Automação de processos complexos** com monitoramento

### Para iniciantes

- **GUI intuitiva**, templates prontos e comunidade ativa em português
- **Documentação abrangente** e exemplos práticos
- **Curva de aprendizado suave** sem necessidade de programação avançada

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Começando

Escolha uma das opções abaixo para começar:

:::tip **Recomendado para iniciantes**
Comece com o **[Primeiros Passos](./primeiros-passos/guia-instalacao)** para aprender os conceitos fundamentais.
:::

:::info **Para desenvolvedores**
Vá direto para **[Integrações e API](../../integracoes)** para integração avançada.
:::

:::note **Para empresas**
Consulte **[Hosting n8n](../../hosting-n8n)** para configuração em produção.
:::

---

## <ion-icon name="document-text-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Estrutura desta Documentação

Esta documentação está organizada nas seguintes seções:

### Primeiros Passos

Aprenda os conceitos fundamentais do n8n passo a passo.

- **[Instalação](./primeiros-passos/guia-instalacao)** - Guia completo de instalação
- **[Conceitos Básicos](./primeiros-passos/conceitos-basicos)** - Fundamentos do n8n
- **[Primeiro Workflow](./primeiros-passos/primeiro-workflow)** - Criar primeiro fluxo

### Guias Avançados

Explore recursos avançados e casos de uso complexos.

- **[Usando n8n](../../usando-n8n)** - Guias práticos para usar a interface
  - [Getting Started](../../usando-n8n/getting-started) - Início rápido
  - [Interface](../../usando-n8n/interface) - Navegação e editor UI
  - [Execuções](../../usando-n8n/execucoes) - Componentes de execução
- **[Lógica e Dados](../../logica-e-dados)** - Conceitos avançados de fluxo
  - [Lógica de Fluxo](../../logica-e-dados/flow-logic) - Error handling, looping, merging
  - [Dados](../../logica-e-dados/data) - Data flow, mapping, structure
- **[IA Avançada](../../advanced-ai)** - Integração com inteligência artificial
- **[Embed](../../embed)** - Implementação e gerenciamento

### Referência da API

Documentação completa da API REST do n8n.

- **[API](../../api)** - Documentação completa da API
  - [Conceitos](../../api/conceitos) - Autenticação, paginação
  - [Ferramentas](../../api/ferramentas) - Playground e recursos
  - [Referência](../../api/referencia) - Documentação técnica

### Deployment

Guias para implantação em diferentes ambientes.

- **[Hosting n8n](../../hosting-n8n)** - Guias para implantação
  - [Instalação](../../hosting-n8n/instalacao) - Desktop, Docker, NPM, Cloud
  - [Configuração](../../hosting-n8n/configuracao) - Variáveis, database, SSL
  - [Segurança](../../hosting-n8n/seguranca) - Autenticação, backup, monitoring
  - [Escalonamento](../../hosting-n8n/escalonamento) - Clustering, load balancing

### Nós (Nodes)

Documentação detalhada de todos os nós disponíveis.

- **[Integrações](../../integracoes)** - Todos os nós disponíveis
  - [Nodes Integrados](../../integracoes/builtin-nodes) - HTTP, dados, lógica, utilitários
  - [App Nodes](../../integracoes/app-nodes) - Comunicação, produtividade, e-commerce, marketing
  - [Trigger Nodes](../../integracoes/trigger-nodes) - Baseados em tempo, eventos, apps
  - [Credential Nodes](../../integracoes/credential-nodes) - API keys, OAuth, autenticação
  - [Community Nodes](../../integracoes/community-nodes) - Nodes da comunidade
  - [Criar Nodes](../../integracoes/criar-nodes) - Desenvolvimento de nodes customizados
- **[Integrações Brasileiras](../../integracoes-br)** - PIX, CNPJ, ViaCEP

### Exemplos

Workflows práticos e casos de uso reais.

- **[Comunidade](../../comunidade)** - Artigos e casos de uso
  - [Automação para Iniciantes](../../comunidade/automacao-iniciantes)
  - [Casos de Uso Avançados](../../comunidade/casos-uso-avancados)
  - [Vídeos](../../comunidade/videos)
- **[Cursos](../../cursos)** - Aprendizado estruturado
  - [Cursos em Vídeo](../../cursos/cursos-em-video)
  - [Cursos em Texto](../../cursos/cursos-em-texto)
- **[Referência](../../referencia)** - Guias e recursos
  - [Guias](../../referencia/guias) - Performance, troubleshooting
  - [Recursos](../../referencia/recursos) - APIs brasileiras, glossário
  - [Histórico](../../referencia/historico) - Changelog

### Recursos Adicionais

- **[Como Contribuir](../../contribuir)** - Guias para contribuição
- **[Release Notes](../../release-notes/index)** - Notas de versão <!-- Temporariamente desabilitado -->
- **[Catálogo](../../catalogo)** - Índice de recursos

---

## <ion-icon name="people-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Compromisso da comunidade brasileira

Este material é produzido **pela comunidade brasileira**, sem vínculo direto com o time oficial do n8n. O objetivo é **ampliar o acesso e contextualizar o uso** dessa ferramenta global no Brasil, seguindo as diretrizes do projeto oficial e incentivando contribuições locais.

### Como contribuir

- **[Editar esta página](../../contribuir/esta-documentacao/entendendo-o-projeto/como-contribuir)**
- **[Reportar um problema](https://github.com/tatyquebralayout/n8n-Doc-pt-BR/issues)**
- **[Participar da discussão](https://discord.gg/n8nbrasil)**

---

> <span style={{fontSize: '18px', fontWeight: 'bold'}}>**Pronto para criar?** Vamos construir seu primeiro workflow juntos!</span>
