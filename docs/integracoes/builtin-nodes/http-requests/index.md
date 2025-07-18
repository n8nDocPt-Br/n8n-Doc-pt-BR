---
title: HTTP Requests
description: Nodes para fazer e receber requisições HTTP no n8n
sidebar_position: 1
keywords: [n8n, http, requests, webhook, api, rest]
---

# <ion-icon name="globe-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> HTTP Requests

Os nodes de **HTTP Requests** são fundamentais para integração com APIs e serviços externos no n8n. Eles permitem que você se comunique com qualquer sistema que tenha uma interface HTTP, seja para enviar dados ou receber notificações.

## Nodes Disponíveis

### <ion-icon name="arrow-up-outline" style={{ fontSize: '20px', color: '#ea4b71' }}></ion-icon> HTTP Request

O node **HTTP Request** é usado para **enviar** requisições HTTP para APIs e serviços externos. É o node mais versátil para integração com sistemas que não possuem nodes dedicados.

**Principais funcionalidades:**
- Suporte a todos os métodos HTTP (GET, POST, PUT, DELETE, etc.)
- Múltiplos tipos de autenticação
- Envio de dados em diferentes formatos (JSON, Form Data, etc.)
- Configuração de headers e parâmetros de query
- Paginação automática
- Tratamento de respostas

**Casos de uso:**
- Integrar com APIs REST
- Enviar dados para sistemas externos
- Fazer consultas a bancos de dados via API
- Upload de arquivos
- Automação de processos web

[Ver documentação completa →](/integracoes/builtin-nodes/http-requests/http-request)

### <ion-icon name="link-outline" style={{ fontSize: '20px', color: '#ea4b71' }}></ion-icon> Webhook

O node **Webhook** é usado para **receber** requisições HTTP de sistemas externos. É um node trigger que pode iniciar workflows baseado em eventos externos.

**Principais funcionalidades:**
- Receber dados de sistemas externos
- Suporte a diferentes métodos HTTP
- Autenticação configurável
- Respostas customizadas
- Parâmetros de rota dinâmicos
- Configuração de CORS

**Casos de uso:**
- Receber notificações de sistemas externos
- Criar endpoints de API
- Integração com webhooks de terceiros
- Automação baseada em eventos
- Construir APIs REST

[Ver documentação completa →](/integracoes/builtin-nodes/http-requests/webhook)

## Diferenças Principais

| Aspecto | HTTP Request | Webhook |
|---------|--------------|---------|
| **Direção** | Enviar dados | Receber dados |
| **Tipo** | Node de ação | Node trigger |
| **Inicia workflow** | Não | Sim |
| **URL** | Configurada pelo usuário | Gerada pelo n8n |
| **Uso principal** | Integração com APIs | Receber notificações |

## Fluxo de Trabalho Típico

### 1. Receber Dados (Webhook)
```
Sistema Externo → Webhook → n8n Workflow
```

### 2. Processar Dados
```
n8n Workflow → Nodes de Processamento → Dados Transformados
```

### 3. Enviar Dados (HTTP Request)
```
n8n Workflow → HTTP Request → Sistema Externo
```

## Exemplos de Integração

### Exemplo 1: Pipeline de Notificações

1. **Webhook** recebe notificação de novo usuário
2. **Set** node processa os dados
3. **HTTP Request** envia para Slack
4. **HTTP Request** salva no banco de dados

### Exemplo 2: API Gateway

1. **Webhook** recebe requisição de API
2. **HTTP Request** consulta banco de dados
3. **Set** node formata resposta
4. **Webhook** retorna dados formatados

### Exemplo 3: Sincronização de Dados

1. **Schedule Trigger** executa periodicamente
2. **HTTP Request** busca dados da API A
3. **HTTP Request** envia dados para API B
4. **HTTP Request** registra log da operação

## Configuração de Segurança

### Autenticação

Ambos os nodes suportam múltiplos métodos de autenticação:

- **Basic Auth**: Usuário e senha
- **Header Auth**: Token no header
- **OAuth2**: Fluxo OAuth completo
- **JWT**: JSON Web Tokens
- **Custom**: Autenticação personalizada

### Validação de Dados

Sempre valide os dados recebidos antes de processá-los:

```javascript
// Exemplo de validação básica
if (!$json.email || !$json.nome) {
  throw new Error('Dados obrigatórios não fornecidos');
}

// Validar formato de email
const emailRegex = /^[^\s@]+@[^\s@]+\.[^\s@]+$/;
if (!emailRegex.test($json.email)) {
  throw new Error('Email inválido');
}
```

### Rate Limiting

Configure limites de taxa para evitar sobrecarga:

```javascript
// Exemplo de rate limiting simples
const delay = 1000; // 1 segundo
await new Promise(resolve => setTimeout(resolve, delay));
```

## Boas Práticas

### Para HTTP Request

1. **Use credenciais predefinidas** quando disponível
2. **Configure timeouts adequados** para evitar travamentos
3. **Implemente retry logic** para falhas temporárias
4. **Valide respostas** antes de processar
5. **Use paginação** para grandes volumes de dados

### Para Webhook

1. **Sempre use autenticação** em produção
2. **Configure lista branca de IPs** quando possível
3. **Valide dados de entrada** antes de processar
4. **Configure CORS** adequadamente
5. **Monitore chamadas** para detectar uso indevido

### Para Ambos

1. **Use HTTPS** para todas as comunicações
2. **Implemente logging** para debug
3. **Configure tratamento de erros** adequado
4. **Teste em ambiente de desenvolvimento** primeiro
5. **Documente endpoints** e formatos de dados

## Troubleshooting

### Problemas Comuns

#### HTTP Request
- **Timeout**: Aumente o timeout ou verifique a conectividade
- **Autenticação**: Verifique credenciais e formato
- **Rate Limiting**: Implemente delays entre requisições
- **Formato de dados**: Verifique Content-Type e estrutura

#### Webhook
- **Não recebe dados**: Verifique se o workflow está ativo
- **Erro 403**: Verifique autenticação e IPs permitidos
- **Payload muito grande**: Comprima dados ou aumente limite
- **CORS**: Configure origens permitidas

### Debug

1. **Use o node Debug Helper** para inspecionar dados
2. **Configure "Incluir Headers e Status"** no HTTP Request
3. **Use URLs de teste** para webhooks
4. **Monitore logs** do n8n
5. **Teste com ferramentas externas** (Postman, curl)

## Próximos Passos

- [Expressões n8n](/logica-e-dados/expressoes) - Usar dados dinâmicos
- [Tratamento de Erros](/logica-e-dados/flow-logic/error-handling) - Lidar com falhas
- [Credenciais](/integracoes/credential-nodes/index) - Configurar autenticação
- [Nodes de Lógica](/integracoes/builtin-nodes/logic-control/index) - Controlar fluxo
- [Nodes de Dados](/integracoes/builtin-nodes/data-processing/index) - Processar dados
