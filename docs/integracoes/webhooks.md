---
title: Webhooks no n8n
description: Aprenda a usar webhooks para integrar o n8n com sistemas externos e criar automações baseadas em eventos
sidebar_position: 1
keywords: [n8n, webhook, trigger, integração, api, eventos]
---

# <ion-icon name="link-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Webhooks no n8n

Webhooks são uma forma poderosa de integrar o n8n com sistemas externos. Eles permitem que aplicações e serviços enviem dados para o n8n quando eventos específicos ocorrem, iniciando workflows automaticamente.

## O que são Webhooks?

Um **webhook** é um mecanismo que permite que uma aplicação envie dados para outra aplicação em tempo real quando um evento específico acontece. No contexto do n8n, webhooks são URLs especiais que podem receber dados de sistemas externos e disparar workflows automaticamente.

### Como funcionam

1. **Evento ocorre** em um sistema externo (ex: novo pedido, mensagem recebida)
2. **Sistema envia dados** para a URL do webhook do n8n
3. **n8n recebe os dados** e inicia o workflow automaticamente
4. **Workflow processa os dados** e executa as ações necessárias

## Vantagens dos Webhooks

### Automação em Tempo Real
Webhooks permitem que você reaja a eventos instantaneamente, sem precisar verificar periodicamente se algo aconteceu.

### Integração Bidirecional
Você pode tanto receber dados de sistemas externos quanto enviar dados para eles, criando integrações completas.

### Flexibilidade
Webhooks funcionam com qualquer sistema que possa fazer requisições HTTP, não apenas com aplicações que têm nodes dedicados no n8n.

### Escalabilidade
Webhooks são eficientes e podem lidar com grandes volumes de eventos sem sobrecarregar o sistema.

## Tipos de Webhooks no n8n

### 1. Webhook Trigger

O **Webhook Trigger** é usado para receber dados de sistemas externos e iniciar workflows. É o tipo mais comum de webhook.

**Características:**
- Inicia workflows automaticamente
- Pode receber dados em diferentes formatos
- Suporta múltiplos métodos HTTP
- Configuração de autenticação
- Respostas customizadas

**Casos de uso:**
- Receber notificações de e-commerce
- Integração com sistemas de pagamento
- Webhooks de redes sociais
- Notificações de sistemas internos

### 2. Webhook como Node de Ação

O **Webhook** também pode ser usado como um node de ação dentro de um workflow, permitindo que você crie endpoints de API dinamicamente.

**Características:**
- Cria endpoints temporários
- Retorna dados processados
- Útil para prototipagem de APIs
- Integração com sistemas que precisam de respostas

## Configuração de Webhooks

### 1. Criar um Webhook Trigger

1. **Adicione um Webhook node** ao seu workflow
2. **Configure o método HTTP** (GET, POST, PUT, etc.)
3. **Defina o caminho** da URL (opcional)
4. **Configure autenticação** se necessário
5. **Ative o workflow** para gerar a URL de produção

### 2. URLs de Webhook

O n8n gera duas URLs para cada webhook:

- **URL de Teste**: Para desenvolvimento e testes
- **URL de Produção**: Para uso em ambiente real

### 3. Configuração de Autenticação

Você pode proteger seus webhooks com diferentes métodos de autenticação:

- **Basic Auth**: Usuário e senha
- **Header Auth**: Token no header
- **JWT Auth**: JSON Web Token
- **Nenhuma**: Sem autenticação (não recomendado para produção)

## Exemplos Práticos

### Exemplo 1: Webhook de E-commerce

**Cenário:** Receber notificações de novos pedidos de uma loja online.

**Configuração:**
```json
{
  "método": "POST",
  "caminho": "/webhook/pedidos",
  "autenticação": "Header Auth",
  "header": "X-API-Key"
}
```

**Dados recebidos:**
```json
{
  "pedido_id": "12345",
  "cliente": {
    "nome": "João Silva",
    "email": "joao@exemplo.com"
  },
  "produtos": [
    {
      "nome": "Produto A",
      "quantidade": 2,
      "preco": 29.90
    }
  ],
  "total": 59.80
}
```

**Workflow:**
1. Receber dados do webhook
2. Validar informações do pedido
3. Enviar confirmação por email
4. Atualizar estoque
5. Registrar no sistema de CRM

### Exemplo 2: Webhook de Pagamento

**Cenário:** Receber confirmações de pagamento do PagSeguro.

**Configuração:**
```json
{
  "método": "POST",
  "caminho": "/webhook/pagamento",
  "autenticação": "JWT Auth"
}
```

**Dados recebidos:**
```json
{
  "event": "PAYMENT.SUCCESS",
  "payment": {
    "id": "PAY-123456789",
    "status": "COMPLETED",
    "amount": {
      "currency": "BRL",
      "total": "100.00"
    },
    "payer": {
      "email": "cliente@exemplo.com"
    }
  }
}
```

**Workflow:**
1. Receber confirmação de pagamento
2. Verificar status do pagamento
3. Atualizar status do pedido
4. Enviar email de confirmação
5. Gerar nota fiscal

### Exemplo 3: API Gateway

**Cenário:** Criar um endpoint de API para consultar dados de clientes.

**Configuração:**
```json
{
  "método": "GET",
  "caminho": "/api/clientes/:id",
  "responder": "Quando o Último Node Terminar"
}
```

**Workflow:**
1. Receber requisição GET
2. Extrair ID do cliente da URL
3. Consultar banco de dados
4. Formatar resposta
5. Retornar dados do cliente

## Boas Práticas

### Segurança

1. **Sempre use autenticação** em produção
2. **Configure lista branca de IPs** quando possível
3. **Valide dados de entrada** antes de processar
4. **Use HTTPS** para todas as comunicações
5. **Monitore chamadas** para detectar uso indevido

### Performance

1. **Processe dados rapidamente** para evitar timeouts
2. **Implemente retry logic** para falhas temporárias
3. **Use filas** para processamento assíncrono
4. **Configure timeouts adequados**
5. **Monitore uso de recursos**

### Manutenção

1. **Documente endpoints** e formatos de dados
2. **Implemente logging** para debug
3. **Configure alertas** para falhas
4. **Teste regularmente** os webhooks
5. **Mantenha versões** dos endpoints

## Troubleshooting

### Problemas Comuns

#### Webhook não recebe dados
- Verifique se o workflow está ativo
- Confirme se está usando a URL correta
- Verifique se não há firewall bloqueando
- Teste com a URL de teste primeiro

#### Erro 403 - Proibido
- Verifique configuração de autenticação
- Confirme se o IP está na lista branca
- Verifique se as credenciais estão corretas

#### Timeout
- Otimize o processamento dos dados
- Configure timeouts adequados
- Use processamento assíncrono quando possível

#### Dados corrompidos
- Valide formato dos dados recebidos
- Verifique encoding (UTF-8)
- Confirme Content-Type correto

### Debug

1. **Use a URL de teste** para ver dados recebidos
2. **Configure logging detalhado**
3. **Use o node Debug Helper**
4. **Teste com ferramentas externas** (Postman, curl)
5. **Monitore logs do n8n**

## Integração com Sistemas Brasileiros

### E-commerce
- **Nuvemshop**: Webhooks para pedidos e produtos
- **WooCommerce**: Webhooks para vendas
- **Mercado Livre**: Webhooks para vendas e mensagens

### Pagamentos
- **PagSeguro**: Webhooks para confirmações de pagamento
- **Mercado Pago**: Webhooks para notificações
- **PayPal**: Webhooks para transações

### Comunicação
- **WhatsApp Business API**: Webhooks para mensagens
- **Telegram Bot API**: Webhooks para comandos
- **Slack**: Webhooks para notificações

### Sistemas Internos
- **ERP**: Webhooks para atualizações
- **CRM**: Webhooks para novos leads
- **Sistema de Estoque**: Webhooks para movimentações

## Próximos Passos

- [Node Webhook](/integracoes/builtin-nodes/http-requests/webhook) - Configuração detalhada
- [Node HTTP Request](/integracoes/builtin-nodes/http-requests/http-request) - Fazer requisições
- [Credenciais Webhook](/integracoes/credential-nodes/webhook) - Configurar autenticação
- [Expressões n8n](/logica-e-dados/expressoes) - Usar dados dinâmicos
- [Tratamento de Erros](/logica-e-dados/flow-logic/error-handling) - Lidar com falhas
