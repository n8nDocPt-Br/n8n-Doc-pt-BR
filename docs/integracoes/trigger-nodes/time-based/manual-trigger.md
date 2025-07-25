---
title: Manual Trigger
description: Guia completo sobre o Manual Trigger no n8n, incluindo configuração, execução manual, exemplos práticos e boas práticas
sidebar_position: 1
keywords: [n8n, manual trigger, execução manual, teste, desenvolvimento, workflow]
---

# <ion-icon name="play-circle-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Manual Trigger

O **Manual Trigger** é o node mais fundamental do n8n, permitindo executar workflows manualmente. É essencial para testes, desenvolvimento e execução sob demanda de automações.

## O que é o Manual Trigger?

O **Manual Trigger** permite:

- **Executar workflows** manualmente
- **Testar automações** durante desenvolvimento
- **Fornecer dados** de entrada para workflows
- **Simular eventos** e cenários específicos
- **Executar workflows** sob demanda
- **Criar workflows** independentes de eventos externos

### Quando Usar o Manual Trigger

- **Desenvolvimento** e teste de workflows
- **Execução manual** de automações
- **Simulação** de dados de entrada
- **Demonstração** de funcionalidades
- **Processamento** sob demanda
- **Workflows** que não dependem de eventos

## Configuração Básica

### Estrutura do Manual Trigger

```javascript
// Manual Trigger - Estrutura básica
{
  "name": "Manual Trigger",
  "description": "Executar workflow manualmente",
  "options": {
    "data": [
      {
        "name": "exemplo",
        "value": "dados de teste"
      }
    ]
  }
}
```

### Configuração de Dados de Entrada

#### 1. Dados Simples

```javascript
// Dados básicos
{
  "name": "nome",
  "value": "João Silva"
}

{
  "name": "idade",
  "value": 30
}

{
  "name": "ativo",
  "value": true
}
```

#### 2. Dados Estruturados

```javascript
// Objeto complexo
{
  "name": "cliente",
  "value": {
    "id": 12345,
    "nome": "João Silva",
    "email": "joao@email.com",
    "telefone": "(11) 99999-9999",
    "ativo": true
  }
}
```

#### 3. Arrays de Dados

```javascript
// Lista de itens
{
  "name": "produtos",
  "value": [
    {
      "id": 1,
      "nome": "Notebook",
      "preco": 2500.00
    },
    {
      "id": 2,
      "nome": "Mouse",
      "preco": 50.00
    }
  ]
}
```

#### 4. Dados com Expressões

```javascript
// Usar expressões para dados dinâmicos
{
  "name": "data_atual",
  "value": "{{ $now.toISOString() }}"
}

{
  "name": "usuario",
  "value": "{{ $env.USER || 'desenvolvedor' }}"
}

{
  "name": "ambiente",
  "value": "{{ $env.NODE_ENV || 'desenvolvimento' }}"
}
```

## Exemplos Práticos

### 1. Teste de Validação de Dados

```javascript
// Manual Trigger - Teste de validação
{
  "name": "Teste Validação",
  "description": "Testar validação de dados de cliente",
  "options": {
    "data": [
      {
        "name": "cliente_valido",
        "value": {
          "nome": "João Silva",
          "email": "joao@email.com",
          "cpf": "12345678901",
          "idade": 25
        }
      },
      {
        "name": "cliente_invalido",
        "value": {
          "nome": "",
          "email": "email_invalido",
          "cpf": "123",
          "idade": 15
        }
      }
    ]
  }
}
```

### 2. Simulação de Pedido

```javascript
// Manual Trigger - Simular pedido
{
  "name": "Simular Pedido",
  "description": "Testar processamento de pedido",
  "options": {
    "data": [
      {
        "name": "pedido",
        "value": {
          "numero": "PED-2024-001",
          "cliente": {
            "id": 12345,
            "nome": "Maria Santos",
            "email": "maria@email.com"
          },
          "itens": [
            {
              "produto_id": 1,
              "nome": "Notebook Dell",
              "quantidade": 1,
              "preco_unitario": 3500.00
            },
            {
              "produto_id": 2,
              "nome": "Mouse Wireless",
              "quantidade": 2,
              "preco_unitario": 80.00
            }
          ],
          "total": 3660.00,
          "status": "pendente"
        }
      }
    ]
  }
}
```

### 3. Teste de API

```javascript
// Manual Trigger - Teste de API
{
  "name": "Teste API",
  "description": "Testar integração com API externa",
  "options": {
    "data": [
      {
        "name": "configuracao_api",
        "value": {
          "url": "https://api.exemplo.com/dados",
          "method": "POST",
          "headers": {
            "Content-Type": "application/json",
            "Authorization": "Bearer token_teste"
          },
          "body": {
            "cliente_id": 12345,
            "acao": "consultar"
          }
        }
      }
    ]
  }
}
```

### 4. Simulação de Evento

```javascript
// Manual Trigger - Simular evento
{
  "name": "Simular Evento",
  "description": "Simular evento de sistema",
  "options": {
    "data": [
      {
        "name": "evento",
        "value": {
          "tipo": "cliente_novo",
          "timestamp": "{{ $now.toISOString() }}",
          "dados": {
            "cliente_id": 67890,
            "nome": "Pedro Costa",
            "email": "pedro@email.com",
            "origem": "site"
          },
          "metadata": {
            "ip": "192.168.1.100",
            "user_agent": "Mozilla/5.0...",
            "referrer": "https://google.com"
          }
        }
      }
    ]
  }
}
```

### 5. Teste de Processamento em Lote

```javascript
// Manual Trigger - Processamento em lote
{
  "name": "Teste Lote",
  "description": "Testar processamento de múltiplos itens",
  "options": {
    "data": [
      {
        "name": "itens_lote",
        "value": [
          {
            "id": 1,
            "nome": "Produto A",
            "categoria": "eletronicos",
            "preco": 100.00
          },
          {
            "id": 2,
            "nome": "Produto B",
            "categoria": "roupas",
            "preco": 50.00
          },
          {
            "id": 3,
            "nome": "Produto C",
            "categoria": "eletronicos",
            "preco": 200.00
          },
          {
            "id": 4,
            "nome": "Produto D",
            "categoria": "livros",
            "preco": 30.00
          }
        ]
      }
    ]
  }
}
```

## Casos de Uso Avançados

### 1. Teste com Diferentes Cenários

```javascript
// Manual Trigger - Múltiplos cenários
{
  "name": "Teste Cenários",
  "description": "Testar diferentes cenários de negócio",
  "options": {
    "data": [
      {
        "name": "cenario_sucesso",
        "value": {
          "tipo": "sucesso",
          "dados": {
            "cliente": "vip",
            "valor": 5000,
            "status": "aprovado"
          }
        }
      },
      {
        "name": "cenario_erro",
        "value": {
          "tipo": "erro",
          "dados": {
            "cliente": "novo",
            "valor": 10000,
            "status": "pendente"
          }
        }
      },
      {
        "name": "cenario_limite",
        "value": {
          "tipo": "limite",
          "dados": {
            "cliente": "regular",
            "valor": 1000,
            "status": "rejeitado"
          }
        }
      }
    ]
  }
}
```

### 2. Simulação de Dados Reais

```javascript
// Manual Trigger - Dados realistas
{
  "name": "Dados Reais",
  "description": "Simular dados de produção",
  "options": {
    "data": [
      {
        "name": "transacao_real",
        "value": {
          "id": "{{ $now.toMillis() }}",
          "cliente": {
            "id": "{{ Math.floor(Math.random() * 10000) }}",
            "nome": "{{ ['João', 'Maria', 'Pedro', 'Ana'][Math.floor(Math.random() * 4)] }} {{ ['Silva', 'Santos', 'Costa', 'Oliveira'][Math.floor(Math.random() * 4)] }}",
            "email": "{{ ['joao', 'maria', 'pedro', 'ana'][Math.floor(Math.random() * 4)] }}@email.com"
          },
          "produto": {
            "id": "{{ Math.floor(Math.random() * 100) }}",
            "nome": "{{ ['Notebook', 'Mouse', 'Teclado', 'Monitor'][Math.floor(Math.random() * 4)] }}",
            "preco": "{{ Math.floor(Math.random() * 1000) + 100 }}"
          },
          "timestamp": "{{ $now.toISOString() }}",
          "status": "{{ ['aprovado', 'pendente', 'rejeitado'][Math.floor(Math.random() * 3)] }}"
        }
      }
    ]
  }
}
```

### 3. Teste de Performance

```javascript
// Manual Trigger - Teste de performance
{
  "name": "Teste Performance",
  "description": "Testar performance com grandes volumes",
  "options": {
    "data": [
      {
        "name": "dados_grande_volume",
        "value": "{{ Array.from({length: 1000}, (_, i) => ({ id: i + 1, nome: `Item ${i + 1}`, valor: Math.random() * 1000 })) }}"
      }
    ]
  }
}
```

### 4. Simulação de Erros

```javascript
// Manual Trigger - Simular erros
{
  "name": "Simular Erros",
  "description": "Testar tratamento de erros",
  "options": {
    "data": [
      {
        "name": "erro_conectividade",
        "value": {
          "tipo": "erro_rede",
          "mensagem": "Timeout na conexão",
          "codigo": 408,
          "retry": true
        }
      },
      {
        "name": "erro_dados",
        "value": {
          "tipo": "erro_validacao",
          "mensagem": "Dados inválidos",
          "campos": ["email", "cpf"],
          "retry": false
        }
      },
      {
        "name": "erro_sistema",
        "value": {
          "tipo": "erro_interno",
          "mensagem": "Erro interno do servidor",
          "codigo": 500,
          "retry": true
        }
      }
    ]
  }
}
```

## Boas Práticas

### 1. Nomenclatura Descritiva

```javascript
// ✅ Bom: Nome descritivo
{
  "name": "Teste Validação Cliente VIP"
}

// ❌ Evitar: Nome genérico
{
  "name": "Teste"
}
```

### 2. Documentação Clara

```javascript
// ✅ Bom: Descrição detalhada
{
  "description": "Testar validação de dados de cliente VIP com valores altos"
}

// ❌ Evitar: Descrição vaga
{
  "description": "Teste"
}
```

### 3. Dados Realistas

```javascript
// ✅ Bom: Dados que simulam produção
{
  "value": {
    "nome": "João Silva",
    "email": "joao.silva@empresa.com",
    "cpf": "12345678901"
  }
}

// ❌ Evitar: Dados irreais
{
  "value": {
    "nome": "teste",
    "email": "teste@teste.com",
    "cpf": "00000000000"
  }
}
```

### 4. Múltiplos Cenários

```javascript
// ✅ Bom: Testar diferentes cenários
{
  "data": [
    { "name": "sucesso", "value": {...} },
    { "name": "erro", "value": {...} },
    { "name": "limite", "value": {...} }
  ]
}

// ❌ Evitar: Apenas um cenário
{
  "data": [
    { "name": "teste", "value": {...} }
  ]
}
```

## Troubleshooting

### Problemas Comuns

#### Workflow não executa
- Verifique se o Manual Trigger está conectado
- Confirme se há dados de entrada
- Teste com dados simples
- Verifique logs de erro

#### Dados não aparecem
- Verifique a estrutura dos dados
- Confirme se os campos estão corretos
- Teste com dados básicos
- Use Debug Helper

#### Performance lenta
- Reduza volume de dados de teste
- Simplifique expressões
- Use dados otimizados
- Monitore recursos

### Debug

```javascript
// Code Node - Debug de Manual Trigger
const debugManualTrigger = (dados) => {
  console.log('=== DEBUG MANUAL TRIGGER ===');
  console.log('Dados de entrada:', dados);
  console.log('Tipo de dados:', typeof dados);
  console.log('É array:', Array.isArray(dados));
  console.log('Número de itens:', Array.isArray(dados) ? dados.length : 1);
  console.log('===========================');
  
  return dados;
};

// Usar após o Manual Trigger
return { json: debugManualTrigger($json) };
```

## Integração com Outros Nodes

### 1. Manual Trigger + Set Node

```javascript
// Manual Trigger - Dados básicos
{
  "name": "dados_cliente",
  "value": {
    "nome": "João Silva",
    "email": "joao@email.com"
  }
}

// Set Node - Enriquecer dados
{
  "mode": "keepAllSet",
  "values": {
    "string": [
      {
        "name": "timestamp_processamento",
        "value": "{{ $now.toISOString() }}"
      },
      {
        "name": "workflow_id",
        "value": "{{ $workflow.id }}"
      }
    ]
  }
}
```

### 2. Manual Trigger + If Node

```javascript
// Manual Trigger - Dados de teste
{
  "name": "dados_teste",
  "value": {
    "tipo": "cliente_vip",
    "valor": 5000
  }
}

// If Node - Roteamento baseado em dados
{
  "condition": "{{ $json.tipo === 'cliente_vip' && $json.valor > 1000 }}",
  "true": "Processamento VIP",
  "false": "Processamento Regular"
}
```

### 3. Manual Trigger + HTTP Request

```javascript
// Manual Trigger - Configuração de API
{
  "name": "config_api",
  "value": {
    "url": "https://api.exemplo.com/dados",
    "method": "POST",
    "headers": {
      "Content-Type": "application/json"
    }
  }
}

// HTTP Request - Usar configuração
{
  "url": "{{ $json.url }}",
  "method": "{{ $json.method }}",
  "headers": "{{ $json.headers }}"
}
```

## Próximos Passos

- [Schedule Trigger](/integracoes/trigger-nodes/time-based/schedule-trigger) - Execução automática
- [Webhook Trigger](/integracoes/trigger-nodes/event-based/webhook-trigger) - Receber dados externos
- [If Node](/integracoes/builtin-nodes/logic-control/if) - Controle de fluxo
- [Set Node](/integracoes/builtin-nodes/data-processing/set) - Manipulação de dados
- [Debugging](/logica-e-dados/flow-logic/debugging) - Técnicas de debug
