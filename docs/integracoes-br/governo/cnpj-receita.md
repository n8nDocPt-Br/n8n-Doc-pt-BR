---
title: CNPJ Receita Federal
description: Integração com a API de consulta de CNPJ da Receita Federal no n8n
sidebar_position: 1
keywords: [n8n, cnpj, receita federal, empresa, validação, brasil, governo]
---

# <ion-icon name="business-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> CNPJ Receita Federal

A **API de CNPJ da Receita Federal** permite consultar dados de empresas brasileiras através do CNPJ (Cadastro Nacional da Pessoa Jurídica). Esta integração permite validar e obter informações completas sobre empresas.

## O que é a API de CNPJ?

A API de CNPJ da Receita Federal é um serviço que fornece dados oficiais de empresas brasileiras. Ela oferece:

- **Dados oficiais** da Receita Federal
- **Informações completas** de empresas
- **Validação** de CNPJ
- **Histórico** de alterações
- **Situação cadastral** atualizada

### Dados Retornados

Para cada CNPJ consultado, a API retorna:

```json
{
  "cnpj": "00.000.000/0001-00",
  "razao_social": "EMPRESA EXEMPLO LTDA",
  "nome_fantasia": "EMPRESA EXEMPLO",
  "data_abertura": "2020-01-01",
  "situacao": "ATIVA",
  "tipo": "MATRIZ",
  "porte": "MICRO EMPRESA",
  "natureza_juridica": "213-5 - Empresário Individual",
  "capital_social": "1000.00",
  "endereco": {
    "logradouro": "Rua Exemplo",
    "numero": "123",
    "complemento": "Sala 1",
    "bairro": "Centro",
    "municipio": "São Paulo",
    "uf": "SP",
    "cep": "01001-000"
  },
  "contato": {
    "telefone": "(11) 1234-5678",
    "email": "contato@empresa.com.br"
  },
  "atividade_principal": {
    "codigo": "47.81-0-00",
    "descricao": "Comércio varejista de artigos do vestuário e acessórios"
  },
  "atividades_secundarias": [
    {
      "codigo": "47.82-8-00",
      "descricao": "Comércio varejista de calçados e artigos de viagem"
    }
  ],
  "quadro_socios": [
    {
      "nome": "João Silva",
      "qualificacao": "Sócio-Administrador",
      "data_entrada": "2020-01-01",
      "cpf_cnpj": "123.456.789-00"
    }
  ]
}
```

## Como Usar a API de CNPJ no n8n

### 1. Configuração Básica

**Node:** HTTP Request
**Método:** GET
**URL:** `https://receitaws.com.br/v1/cnpj/{{ $json.cnpj }}`

### 2. Parâmetros

- **cnpj**: CNPJ a ser consultado (formato: 00.000.000/0001-00 ou 00000000000100)

### 3. Exemplo de Configuração

```javascript
// Configuração do HTTP Request
Method: GET
URL: https://receitaws.com.br/v1/cnpj/{{ $json.cnpj }}
Headers: {
  "Accept": "application/json"
}
```

## Casos de Uso

### 1. Validação de Empresas

Validar se um CNPJ existe e está ativo:

```javascript
// Workflow: Validação de CNPJ
Webhook → HTTP Request (CNPJ) → If (CNPJ válido) → Set → HTTP Request (Salvar)
```

**Configuração:**
- **HTTP Request**: Consulta CNPJ
- **If**: Verifica se `$json.situacao === "ATIVA"`
- **Set**: Formata dados da empresa
- **HTTP Request**: Salva dados validados

### 2. Due Diligence

Análise completa de empresas para processos de due diligence:

```javascript
// Workflow: Due Diligence
Manual Trigger → HTTP Request (CNPJ) → Code (Análise) → Relatório
```

**Configuração:**
- **Manual Trigger**: Entrada do CNPJ
- **HTTP Request**: Consulta CNPJ
- **Code**: Análise de riscos e compliance
- **Relatório**: Gera relatório completo

### 3. Cadastro de Fornecedores

Validar fornecedores durante processo de cadastro:

```javascript
// Workflow: Cadastro de Fornecedor
Webhook (Novo Fornecedor) → HTTP Request (CNPJ) → Validação → Aprovação
```

**Configuração:**
- **Webhook**: Recebe dados do fornecedor
- **HTTP Request**: Consulta CNPJ
- **Validação**: Verifica situação e dados
- **Aprovação**: Aprova ou rejeita cadastro

### 4. Relatórios Fiscais

Gerar relatórios fiscais com dados oficiais:

```javascript
// Workflow: Relatório Fiscal
Schedule Trigger → HTTP Request (CNPJ) → Agregação → Relatório
```

**Configuração:**
- **Schedule Trigger**: Executa periodicamente
- **HTTP Request**: Consulta CNPJs
- **Agregação**: Consolida dados
- **Relatório**: Gera relatório fiscal

## Exemplos Práticos

### Exemplo 1: Validação de CNPJ em E-commerce

**Cenário:** Validar CNPJ de empresa durante cadastro de marketplace.

**Workflow:**
```
Webhook (Cadastro) → HTTP Request (CNPJ) → If (CNPJ Válido) → Aprovar Cadastro
                ↓
            CNPJ Inválido → Rejeitar Cadastro
```

**Configuração:**
```javascript
// HTTP Request - CNPJ
Method: GET
URL: https://receitaws.com.br/v1/cnpj/{{ $json.cnpj }}

// If - Validação
Condition: $json.situacao === "ATIVA" && $json.tipo === "MATRIZ"

// Set - Dados da Empresa
{
  "cnpj": "{{ $('CNPJ').json.cnpj }}",
  "razao_social": "{{ $('CNPJ').json.razao_social }}",
  "nome_fantasia": "{{ $('CNPJ').json.nome_fantasia }}",
  "situacao": "{{ $('CNPJ').json.situacao }}",
  "porte": "{{ $('CNPJ').json.porte }}",
  "endereco": "{{ $('CNPJ').json.endereco.logradouro }}, {{ $('CNPJ').json.endereco.numero }}",
  "cidade": "{{ $('CNPJ').json.endereco.municipio }}",
  "estado": "{{ $('CNPJ').json.endereco.uf }}"
}
```

### Exemplo 2: Due Diligence Automatizada

**Cenário:** Análise automática de empresas para investimento.

**Workflow:**
```
Manual Trigger → HTTP Request (CNPJ) → Code (Análise) → Switch (Risco) → Relatório
```

**Configuração:**
```javascript
// Manual Trigger - Entrada
{
  "cnpj": "00.000.000/0001-00"
}

// HTTP Request - CNPJ
Method: GET
URL: https://receitaws.com.br/v1/cnpj/{{ $json.cnpj }}

// Code - Análise de Risco
const data = $json;
const risco = {
  baixo: 0,
  medio: 0,
  alto: 0
};

// Análise por situação
if (data.situacao !== "ATIVA") {
  risco.alto += 3;
}

// Análise por porte
if (data.porte === "MICRO EMPRESA") {
  risco.medio += 1;
} else if (data.porte === "PEQUENA EMPRESA") {
  risco.baixo += 1;
}

// Análise por capital social
const capital = parseFloat(data.capital_social);
if (capital < 10000) {
  risco.alto += 2;
} else if (capital < 100000) {
  risco.medio += 1;
} else {
  risco.baixo += 1;
}

return {
  json: {
    ...data,
    analise_risco: {
      pontuacao: risco,
      nivel: risco.alto > risco.medio ? "ALTO" : risco.medio > risco.baixo ? "MEDIO" : "BAIXO"
    }
  }
};
```

### Exemplo 3: Cadastro de Fornecedores

**Cenário:** Validar fornecedores automaticamente.

**Workflow:**
```
Webhook (Fornecedor) → HTTP Request (CNPJ) → Validação → Aprovação → Notificação
```

**Configuração:**
```javascript
// HTTP Request - CNPJ
Method: GET
URL: https://receitaws.com.br/v1/cnpj/{{ $json.cnpj }}

// Code - Validação Completa
const cnpjData = $json;
const fornecedor = $('Webhook').json;

const validacoes = {
  cnpj_valido: cnpjData.cnpj === fornecedor.cnpj,
  situacao_ativa: cnpjData.situacao === "ATIVA",
  endereco_confere: cnpjData.endereco.municipio === fornecedor.cidade,
  telefone_confere: cnpjData.contato.telefone === fornecedor.telefone
};

const aprovado = Object.values(validacoes).every(v => v);

return {
  json: {
    fornecedor: fornecedor,
    dados_receita: cnpjData,
    validacoes: validacoes,
    aprovado: aprovado,
    motivo_rejeicao: aprovado ? null : Object.keys(validacoes).filter(k => !validacoes[k])
  }
};
```

## Tratamento de Erros

### 1. CNPJ Não Encontrado

Quando o CNPJ não existe:

```json
{
  "erro": "CNPJ não encontrado"
}
```

**Tratamento:**
```javascript
// If - CNPJ Inválido
Condition: $json.erro !== undefined

// Set - Mensagem de Erro
{
  "erro": true,
  "mensagem": "CNPJ não encontrado na Receita Federal.",
  "cnpj_informado": "{{ $('Manual Trigger').json.cnpj }}"
}
```

### 2. Rate Limiting

A API tem limites de uso:

```javascript
// Code - Tratamento de Rate Limit
try {
  // Fazer requisição
} catch (error) {
  if (error.httpCode === 429) {
    // Aguardar e tentar novamente
    await new Promise(resolve => setTimeout(resolve, 5000));
    // Retry logic
  }
}
```

### 3. CNPJ Inválido

Para CNPJs com formato inválido:

```javascript
// Code - Validação de Formato
const cnpj = $json.cnpj.replace(/\D/g, '');
if (cnpj.length !== 14) {
  throw new Error('CNPJ deve ter 14 dígitos');
}

// Validação de dígitos verificadores
function validarCNPJ(cnpj) {
  // Implementação da validação
  return true;
}
```

## Boas Práticas

### 1. Formatação de CNPJ

Sempre formate o CNPJ antes da consulta:

```javascript
// Code - Formatação de CNPJ
const cnpj = $json.cnpj.replace(/\D/g, ''); // Remove caracteres não numéricos
const cnpjFormatado = cnpj.replace(/^(\d{2})(\d{3})(\d{3})(\d{4})(\d{2})$/, '$1.$2.$3/$4-$5');
```

### 2. Cache de Consultas

Implemente cache para CNPJs consultados frequentemente:

```javascript
// Code - Cache Simples
const cnpj = $json.cnpj;
const cacheKey = `cnpj_${cnpj}`;

// Verificar cache (implementar conforme sua solução)
const cached = await getFromCache(cacheKey);
if (cached) {
  return { json: cached };
}

// Consultar API
const response = await fetch(`https://receitaws.com.br/v1/cnpj/${cnpj}`);
const data = await response.json();

// Salvar no cache
await saveToCache(cacheKey, data);

return { json: data };
```

### 3. Validação de Dados

Sempre valide os dados retornados:

```javascript
// Code - Validação de Dados
const data = $json;

if (!data.cnpj || !data.razao_social) {
  throw new Error('Dados incompletos retornados pela API');
}

// Validar formato do CNPJ
const cnpjRegex = /^\d{2}\.\d{3}\.\d{3}\/\d{4}-\d{2}$/;
if (!cnpjRegex.test(data.cnpj)) {
  throw new Error('Formato de CNPJ inválido');
}
```

### 4. Tratamento de Timeout

Configure timeouts adequados:

```javascript
// HTTP Request - Configuração
Method: GET
URL: https://receitaws.com.br/v1/cnpj/{{ $json.cnpj }}
Timeout: 30000 // 30 segundos
```

## Troubleshooting

### Problemas Comuns

#### API não responde
- Verifique conectividade com internet
- Confirme se a API está funcionando
- Teste com CNPJ conhecido
- Verifique formato do CNPJ

#### CNPJ não encontrado
- Confirme se o CNPJ existe
- Verifique formato (00.000.000/0001-00)
- Teste com CNPJs de diferentes tipos
- Consulte site da Receita Federal

#### Rate limiting
- Implemente delays entre consultas
- Use cache para consultas frequentes
- Configure retry com backoff
- Monitore limites da API

### Debug

1. **Use o node Debug Helper** para inspecionar dados
2. **Teste com CNPJs conhecidos** (ex: 00.000.000/0001-00)
3. **Verifique formato** do CNPJ de entrada
4. **Monitore logs** de erro
5. **Teste a API diretamente** no navegador

## Limitações e Considerações

### Limitações da API

- **Rate limiting**: Limite de consultas por minuto
- **Dados da Receita**: Depende da atualização da Receita Federal
- **CNPJ não encontrado**: Alguns CNPJs podem não estar disponíveis
- **Formato fixo**: Apenas CNPJs brasileiros

### Considerações de Performance

- **Cache**: Implemente cache para consultas frequentes
- **Batch**: Evite múltiplas consultas simultâneas
- **Timeout**: Configure timeouts adequados
- **Retry**: Implemente retry logic para falhas

### Compliance e Segurança

- **LGPD**: Implemente conformidade com LGPD
- **Auditoria**: Mantenha logs de consultas
- **Acesso**: Controle acesso aos dados
- **Retenção**: Configure políticas de retenção

## Próximos Passos

- [HTTP Request](/integracoes/builtin-nodes/http-requests/http-request) - Fazer requisições HTTP
- [Expressões n8n](/logica-e-dados/expressoes) - Usar dados dinâmicos
- [Tratamento de Erros](/logica-e-dados/flow-logic/error-handling) - Lidar com falhas
- [Integrações Brasileiras](/integracoes-br/index) - Outras integrações brasileiras
- [Compliance LGPD](/privacidade-seguranca/lgpd-compliance) - Conformidade com LGPD
