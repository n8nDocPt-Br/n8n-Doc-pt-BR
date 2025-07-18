---
sidebar_position: 3
title: Backup e Recovery
description: Como implementar estratégias de backup e recuperação para n8n
keywords: [n8n, backup, recovery, restauração, continuidade, dados]
---


# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup e Recovery

Este documento ensina como **implementar backup e recovery** para n8n, incluindo backup automático de workflows, export/import de credenciais, versionamento de configurações, recuperação point-in-time, testes de restauração, e estratégias de disaster recovery que garantem proteção completa dos dados críticos e continuidade operacional mesmo em cenários catastróficos de perda de dados ou infraestrutura.

## <ion-icon name="school-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> O que você vai aprender

- Estratégias de backup 3-2-1
- Scripts de automação
- Recuperação point-in-time
- Disaster recovery
- Testes de restauração

---

## <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Estratégia de Backup 3-2-1

### Princípio 3-2-1

#### **Definição da Estratégia**

- **3 cópias** dos dados (original + 2 backups)
- **2 tipos** de mídia diferentes (disco + nuvem)
- **1 cópia** fora do local (backup remoto)

#### **Implementação**

```bash
# <ion-icon name="folder-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Estrutura de backup 3-2-1
/backups/n8n/
├── local/                    # Backup local (disco)
│   ├── daily/               # Backups diários
│   ├── weekly/              # Backups semanais
│   └── monthly/             # Backups mensais
├── cloud/                   # Backup na nuvem
│   ├── s3/                  # Amazon S3
│   ├── gcs/                 # Google Cloud Storage
│   └── azure/               # Azure Blob Storage
└── offsite/                 # Backup fora do local
    ├── remote-server/       # Servidor remoto
    └── tape/                # Fita (se aplicável)
```

---

## <ion-icon name="analytics-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de Banco de Dados

### PostgreSQL

#### **Script de Backup PostgreSQL**

```bash
#!/bin/bash
# <ion-icon name="server-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-postgres.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
DB_HOST="localhost"
DB_PORT="5432"
DB_NAME="n8n"
DB_USER="n8n"
DB_PASSWORD="senha_banco"
BACKUP_DIR="/backups/n8n/database"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/n8n_db_$DATE.sql"
RETENTION_DAYS=30

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Criar diretório se não existir
mkdir -p $BACKUP_DIR

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup completo
echo "Iniciando backup do PostgreSQL..."
pg_dump -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME > $BACKUP_FILE

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o backup foi bem-sucedido
if [ $? -eq 0 ]; then
    echo "Backup criado com sucesso: $BACKUP_FILE"
    
    # Comprimir backup
    gzip $BACKUP_FILE
    echo "Backup comprimido: $BACKUP_FILE.gz"
    
    # Calcular tamanho do backup
    BACKUP_SIZE=$(du -h "$BACKUP_FILE.gz" | cut -f1)
    echo "Tamanho do backup: $BACKUP_SIZE"
    
    # Remover backups antigos
    find $BACKUP_DIR -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete
    echo "Backups antigos removidos (mais de $RETENTION_DAYS dias)"
    
    # Log do backup
    echo "$(date): Backup PostgreSQL criado - $BACKUP_FILE.gz ($BACKUP_SIZE)" >> /var/log/n8n/backup.log
    
else
    echo "ERRO: Falha no backup do PostgreSQL"
    exit 1
fi
```

#### **Backup Incremental**

```bash
#!/bin/bash
# <ion-icon name="server-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-postgres-incremental.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
DB_HOST="localhost"
DB_PORT="5432"
DB_NAME="n8n"
DB_USER="n8n"
BACKUP_DIR="/backups/n8n/database/incremental"
DATE=$(date +%Y%m%d_%H%M%S)

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup incremental usando WAL
pg_basebackup -h $DB_HOST -p $DB_PORT -U $DB_USER \
  -D $BACKUP_DIR/base_$DATE \
  -Ft -z -P

echo "Backup incremental criado: $BACKUP_DIR/base_$DATE"
```

### MySQL

#### **Script de Backup MySQL**

```bash
#!/bin/bash
# <ion-icon name="server-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-mysql.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
DB_HOST="localhost"
DB_PORT="3306"
DB_NAME="n8n"
DB_USER="n8n"
DB_PASSWORD="senha_banco"
BACKUP_DIR="/backups/n8n/database"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/n8n_db_$DATE.sql"
RETENTION_DAYS=30

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Criar diretório se não existir
mkdir -p $BACKUP_DIR

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup completo
echo "Iniciando backup do MySQL..."
mysqldump -h $DB_HOST -P $DB_PORT -u $DB_USER -p$DB_PASSWORD \
  --single-transaction \
  --routines \
  --triggers \
  --events \
  $DB_NAME > $BACKUP_FILE

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o backup foi bem-sucedido
if [ $? -eq 0 ]; then
    echo "Backup criado com sucesso: $BACKUP_FILE"
    
    # Comprimir backup
    gzip $BACKUP_FILE
    echo "Backup comprimido: $BACKUP_FILE.gz"
    
    # Calcular tamanho do backup
    BACKUP_SIZE=$(du -h "$BACKUP_FILE.gz" | cut -f1)
    echo "Tamanho do backup: $BACKUP_SIZE"
    
    # Remover backups antigos
    find $BACKUP_DIR -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete
    echo "Backups antigos removidos (mais de $RETENTION_DAYS dias)"
    
    # Log do backup
    echo "$(date): Backup MySQL criado - $BACKUP_FILE.gz ($BACKUP_SIZE)" >> /var/log/n8n/backup.log
    
else
    echo "ERRO: Falha no backup do MySQL"
    exit 1
fi
```

---

## <ion-icon name="shield-checkmark-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de Workflows e Credenciais

### Backup via API

#### **Script de Backup de Workflows**

```bash
#!/bin/bash
# <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-workflows.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
N8N_URL="https://seu-n8n.com"
API_KEY="sua_api_key"
BACKUP_DIR="/backups/n8n/workflows"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/workflows_$DATE.json"
RETENTION_DAYS=30

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Criar diretório se não existir
mkdir -p $BACKUP_DIR

# <ion-icon name="code-slash-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de workflows via API
echo "Iniciando backup de workflows..."
curl -H "X-N8N-API-KEY: $API_KEY" \
  "$N8N_URL/api/v1/workflows" \
  | jq '.' > $BACKUP_FILE

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o backup foi bem-sucedido
if [ $? -eq 0 ]; then
    echo "Backup de workflows criado: $BACKUP_FILE"
    
    # Comprimir backup
    gzip $BACKUP_FILE
    echo "Backup comprimido: $BACKUP_FILE.gz"
    
    # Calcular tamanho do backup
    BACKUP_SIZE=$(du -h "$BACKUP_FILE.gz" | cut -f1)
    echo "Tamanho do backup: $BACKUP_SIZE"
    
    # Remover backups antigos
    find $BACKUP_DIR -name "*.json.gz" -mtime +$RETENTION_DAYS -delete
    echo "Backups antigos removidos (mais de $RETENTION_DAYS dias)"
    
    # Log do backup
    echo "$(date): Backup de workflows criado - $BACKUP_FILE.gz ($BACKUP_SIZE)" >> /var/log/n8n/backup.log
    
else
    echo "ERRO: Falha no backup de workflows"
    exit 1
fi
```

#### **Script de Backup de Credenciais**

```bash
#!/bin/bash
# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-credentials.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
N8N_URL="https://seu-n8n.com"
API_KEY="sua_api_key"
BACKUP_DIR="/backups/n8n/credentials"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/credentials_$DATE.json"
RETENTION_DAYS=30

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Criar diretório se não existir
mkdir -p $BACKUP_DIR

# <ion-icon name="code-slash-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de credenciais via API
echo "Iniciando backup de credenciais..."
curl -H "X-N8N-API-KEY: $API_KEY" \
  "$N8N_URL/api/v1/credentials" \
  | jq '.' > $BACKUP_FILE

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o backup foi bem-sucedido
if [ $? -eq 0 ]; then
    echo "Backup de credenciais criado: $BACKUP_FILE"
    
    # Comprimir backup
    gzip $BACKUP_FILE
    echo "Backup comprimido: $BACKUP_FILE.gz"
    
    # Calcular tamanho do backup
    BACKUP_SIZE=$(du -h "$BACKUP_FILE.gz" | cut -f1)
    echo "Tamanho do backup: $BACKUP_SIZE"
    
    # Remover backups antigos
    find $BACKUP_DIR -name "*.json.gz" -mtime +$RETENTION_DAYS -delete
    echo "Backups antigos removidos (mais de $RETENTION_DAYS dias)"
    
    # Log do backup
    echo "$(date): Backup de credenciais criado - $BACKUP_FILE.gz ($BACKUP_SIZE)" >> /var/log/n8n/backup.log
    
else
    echo "ERRO: Falha no backup de credenciais"
    exit 1
fi
```

### Backup de Volumes Docker

#### **Script de Backup de Volumes**

```bash
#!/bin/bash
# <ion-icon name="cloud-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-docker-volumes.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
VOLUME_NAME="n8n_data"
BACKUP_DIR="/backups/n8n/volumes"
DATE=$(date +%Y%m%d_%H%M%S)
BACKUP_FILE="$BACKUP_DIR/n8n_volume_$DATE.tar.gz"
RETENTION_DAYS=30

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Criar diretório se não existir
mkdir -p $BACKUP_DIR

# <ion-icon name="person-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Parar container n8n
echo "Parando container n8n..."
docker stop n8n

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup do volume
echo "Iniciando backup do volume Docker..."
docker run --rm -v $VOLUME_NAME:/data -v $BACKUP_DIR:/backup \
  alpine tar czf /backup/n8n_volume_$DATE.tar.gz -C /data .

# <ion-icon name="person-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Reiniciar container n8n
echo "Reiniciando container n8n..."
docker start n8n

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o backup foi bem-sucedido
if [ $? -eq 0 ]; then
    echo "Backup do volume criado: $BACKUP_FILE"
    
    # Calcular tamanho do backup
    BACKUP_SIZE=$(du -h "$BACKUP_FILE" | cut -f1)
    echo "Tamanho do backup: $BACKUP_SIZE"
    
    # Remover backups antigos
    find $BACKUP_DIR -name "*.tar.gz" -mtime +$RETENTION_DAYS -delete
    echo "Backups antigos removidos (mais de $RETENTION_DAYS dias)"
    
    # Log do backup
    echo "$(date): Backup de volume criado - $BACKUP_FILE ($BACKUP_SIZE)" >> /var/log/n8n/backup.log
    
else
    echo "ERRO: Falha no backup do volume"
    exit 1
fi
```

---

## <ion-icon name="cloud-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup na Nuvem

### Amazon S3

#### **Script de Backup para S3**

```bash
#!/bin/bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-to-s3.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
S3_BUCKET="n8n-backups"
S3_REGION="us-east-1"
BACKUP_DIR="/backups/n8n"
DATE=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=90

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup completo para S3
echo "Iniciando backup para S3..."

# <ion-icon name="analytics-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de banco de dados
aws s3 cp $BACKUP_DIR/database/n8n_db_$DATE.sql.gz \
  s3://$S3_BUCKET/database/n8n_db_$DATE.sql.gz \
  --region $S3_REGION

# <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de workflows
aws s3 cp $BACKUP_DIR/workflows/workflows_$DATE.json.gz \
  s3://$S3_BUCKET/workflows/workflows_$DATE.json.gz \
  --region $S3_REGION

# <ion-icon name="shield-checkmark-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de credenciais
aws s3 cp $BACKUP_DIR/credentials/credentials_$DATE.json.gz \
  s3://$S3_BUCKET/credentials/credentials_$DATE.json.gz \
  --region $S3_REGION

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de volumes
aws s3 cp $BACKUP_DIR/volumes/n8n_volume_$DATE.tar.gz \
  s3://$S3_BUCKET/volumes/n8n_volume_$DATE.tar.gz \
  --region $S3_REGION

# <ion-icon name="settings-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurar lifecycle para remoção automática
aws s3api put-bucket-lifecycle-configuration \
  --bucket $S3_BUCKET \
  --lifecycle-configuration '{
    "Rules": [
      {
        "ID": "DeleteOldBackups",
        "Status": "Enabled",
        "Filter": {
          "Prefix": ""
        },
        "Expiration": {
          "Days": '$RETENTION_DAYS'
        }
      }
    ]
  }'

echo "Backup para S3 concluído"
```

### Google Cloud Storage

#### **Script de Backup para GCS**

```bash
#!/bin/bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> backup-to-gcs.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
GCS_BUCKET="n8n-backups"
BACKUP_DIR="/backups/n8n"
DATE=$(date +%Y%m%d_%H%M%S)
RETENTION_DAYS=90

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup completo para GCS
echo "Iniciando backup para Google Cloud Storage..."

# <ion-icon name="analytics-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de banco de dados
gsutil cp $BACKUP_DIR/database/n8n_db_$DATE.sql.gz \
  gs://$GCS_BUCKET/database/n8n_db_$DATE.sql.gz

# <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de workflows
gsutil cp $BACKUP_DIR/workflows/workflows_$DATE.json.gz \
  gs://$GCS_BUCKET/workflows/workflows_$DATE.json.gz

# <ion-icon name="shield-checkmark-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de credenciais
gsutil cp $BACKUP_DIR/credentials/credentials_$DATE.json.gz \
  gs://$GCS_BUCKET/credentials/credentials_$DATE.json.gz

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de volumes
gsutil cp $BACKUP_DIR/volumes/n8n_volume_$DATE.tar.gz \
  gs://$GCS_BUCKET/volumes/n8n_volume_$DATE.tar.gz

# <ion-icon name="settings-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurar lifecycle para remoção automática
gsutil lifecycle set lifecycle.json gs://$GCS_BUCKET

echo "Backup para GCS concluído"
```

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Recuperação e Restauração

### Restauração PostgreSQL

#### **Script de Restauração**

```bash
#!/bin/bash
# <ion-icon name="server-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> restore-postgres.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
DB_HOST="localhost"
DB_PORT="5432"
DB_NAME="n8n"
DB_USER="n8n"
BACKUP_FILE="$1"

if [ -z "$BACKUP_FILE" ]; then
    echo "Uso: $0 <arquivo_backup>"
    exit 1
fi

# <ion-icon name="grid-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o arquivo existe
if [ ! -f "$BACKUP_FILE" ]; then
    echo "ERRO: Arquivo de backup não encontrado: $BACKUP_FILE"
    exit 1
fi

echo "Iniciando restauração do PostgreSQL..."

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Parar n8n
echo "Parando n8n..."
docker stop n8n

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Fazer backup antes da restauração
echo "Criando backup de segurança..."
pg_dump -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME > \
  /backups/n8n/pre_restore_backup_$(date +%Y%m%d_%H%M%S).sql

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Descomprimir backup se necessário
if [[ "$BACKUP_FILE" == *.gz ]]; then
    echo "Descomprimindo backup..."
    gunzip -c "$BACKUP_FILE" > "${BACKUP_FILE%.gz}"
    BACKUP_FILE="${BACKUP_FILE%.gz}"
fi

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Restaurar backup
echo "Restaurando backup..."
psql -h $DB_HOST -p $DB_PORT -U $DB_USER -d $DB_NAME < "$BACKUP_FILE"

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se a restauração foi bem-sucedida
if [ $? -eq 0 ]; then
    echo "Restauração concluída com sucesso"
    
    # Reiniciar n8n
    echo "Reiniciando n8n..."
    docker start n8n
    
    # Log da restauração
    echo "$(date): Restauração PostgreSQL concluída - $BACKUP_FILE" >> /var/log/n8n/restore.log
    
else
    echo "ERRO: Falha na restauração"
    exit 1
fi
```

### Restauração de Workflows

#### **Script de Restauração de Workflows**

```bash
#!/bin/bash
# <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> restore-workflows.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
N8N_URL="https://seu-n8n.com"
API_KEY="sua_api_key"
BACKUP_FILE="$1"

if [ -z "$BACKUP_FILE" ]; then
    echo "Uso: $0 <arquivo_backup>"
    exit 1
fi

# <ion-icon name="grid-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Verificar se o arquivo existe
if [ ! -f "$BACKUP_FILE" ]; then
    echo "ERRO: Arquivo de backup não encontrado: $BACKUP_FILE"
    exit 1
fi

echo "Iniciando restauração de workflows..."

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Descomprimir backup se necessário
if [[ "$BACKUP_FILE" == *.gz ]]; then
    echo "Descomprimindo backup..."
    gunzip -c "$BACKUP_FILE" > "${BACKUP_FILE%.gz}"
    BACKUP_FILE="${BACKUP_FILE%.gz}"
fi

# <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Restaurar workflows
echo "Restaurando workflows..."
jq -c '.[]' "$BACKUP_FILE" | while read workflow; do
    curl -X POST \
      -H "X-N8N-API-KEY: $API_KEY" \
      -H "Content-Type: application/json" \
      -d "$workflow" \
      "$N8N_URL/api/v1/workflows"
    
    if [ $? -eq 0 ]; then
        echo "Workflow restaurado com sucesso"
    else
        echo "ERRO: Falha na restauração do workflow"
    fi
done

echo "Restauração de workflows concluída"

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Log da restauração
echo "$(date): Restauração de workflows concluída - $BACKUP_FILE" >> /var/log/n8n/restore.log
```

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Disaster Recovery

### Plano de Disaster Recovery

#### **Cenários de Recuperação**

```bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Cenários de DR documentados
DR_SCENARIOS=(
    "Perda de servidor principal"
    "Corrupção de banco de dados"
    "Falha de storage"
    "Desastre natural"
    "Ataque cibernético"
    "Erro humano"
)
```

#### **RTO e RPO**

```bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Objetivos de Recuperação
RTO_PRIMARY=4      # 4 horas para recuperação
RTO_SECONDARY=24   # 24 horas para recuperação secundária
RPO_PRIMARY=1      # 1 hora de perda de dados máxima
RPO_SECONDARY=24   # 24 horas de perda de dados máxima
```

### Recuperação de Infraestrutura

#### **Script de Recuperação Completa**

```bash
#!/bin/bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> disaster-recovery.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
PRIMARY_SERVER="n8n-primary.empresa.com"
BACKUP_SERVER="n8n-backup.empresa.com"
S3_BUCKET="n8n-backups"
LATEST_BACKUP=$(aws s3 ls s3://$S3_BUCKET/database/ | sort | tail -1 | awk '{print $4}')

echo "Iniciando Disaster Recovery..."

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> 1. Verificar conectividade
echo "Verificando conectividade..."
if ! ping -c 3 $PRIMARY_SERVER > /dev/null; then
    echo "Servidor primário indisponível, iniciando DR..."
    
    # 2. Provisionar servidor de backup
    echo "Provisionando servidor de backup..."
    # Aqui você incluiria comandos para provisionar infraestrutura
    
    # 3. Restaurar banco de dados
    echo "Restaurando banco de dados..."
    aws s3 cp s3://$S3_BUCKET/database/$LATEST_BACKUP /tmp/
    gunzip /tmp/$LATEST_BACKUP
    psql -h localhost -U n8n -d n8n < /tmp/${LATEST_BACKUP%.gz}
    
    # 4. Restaurar workflows
    echo "Restaurando workflows..."
    aws s3 cp s3://$S3_BUCKET/workflows/ /tmp/workflows/ --recursive
    
    # 5. Configurar DNS
    echo "Atualizando DNS..."
    # Comandos para atualizar DNS
    
    # 6. Verificar funcionalidade
    echo "Verificando funcionalidade..."
    curl -f https://$BACKUP_SERVER/healthz
    
    if [ $? -eq 0 ]; then
        echo "Disaster Recovery concluído com sucesso"
        # Notificar equipe
        curl -X POST $WEBHOOK_URL \
          -H "Content-type: application/json" \
          -d '{"text":"✅ Disaster Recovery concluído - n8n disponível em backup"}'
    else
        echo "ERRO: Falha no Disaster Recovery"
        exit 1
    fi
else
    echo "Servidor primário está funcionando"
fi
```

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Testes de Restauração

### Script de Teste

#### **Teste Automatizado de Restauração**

```bash
#!/bin/bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> test-restore.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
TEST_ENV="test-restore"
BACKUP_FILE="$1"
TEST_DB="n8n_test"

if [ -z "$BACKUP_FILE" ]; then
    echo "Uso: $0 <arquivo_backup>"
    exit 1
fi

echo "Iniciando teste de restauração..."

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> 1. Criar ambiente de teste
echo "Criando ambiente de teste..."
docker run -d --name $TEST_ENV \
  -e DB_TYPE=postgresdb \
  -e DB_POSTGRESDB_HOST=localhost \
  -e DB_POSTGRESDB_DATABASE=$TEST_DB \
  -p 5679:5678 \
  n8nio/n8n:latest

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> 2. Aguardar inicialização
echo "Aguardando inicialização..."
sleep 30

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> 3. Restaurar backup
echo "Restaurando backup de teste..."
gunzip -c "$BACKUP_FILE" | psql -h localhost -U n8n -d $TEST_DB

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> 4. Verificar restauração
echo "Verificando restauração..."
WORKFLOW_COUNT=$(curl -s http://localhost:5679/api/v1/workflows | jq '. | length')
echo "Workflows restaurados: $WORKFLOW_COUNT"

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> 5. Testar funcionalidade
echo "Testando funcionalidade..."
curl -f http://localhost:5679/healthz

if [ $? -eq 0 ]; then
    echo "✅ Teste de restauração PASSOU"
    # Limpar ambiente de teste
    docker stop $TEST_ENV
    docker rm $TEST_ENV
    psql -h localhost -U n8n -c "DROP DATABASE $TEST_DB;"
else
    echo "❌ Teste de restauração FALHOU"
    exit 1
fi
```

---

## <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Automação com Cron

### Configuração de Cron Jobs

#### **Crontab Configuração**

```bash
# <ion-icon name="time-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Adicionar ao crontab (crontab -e)

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup diário às 2h da manhã
0 2 * * * /path/to/backup-postgres.sh

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup semanal completo aos domingos às 3h
0 3 * * 0 /path/to/backup-complete.sh

# <ion-icon name="git-branch-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup de workflows a cada 6 horas
0 */6 * * * /path/to/backup-workflows.sh

# <ion-icon name="cloud-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Backup para nuvem diário às 4h
0 4 * * * /path/to/backup-to-s3.sh

# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Teste de restauração semanal aos sábados às 5h
0 5 * * 6 /path/to/test-restore.sh

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Limpeza de backups antigos diariamente às 6h
0 6 * * * /path/to/cleanup-old-backups.sh
```

#### **Script de Limpeza**

```bash
#!/bin/bash
# <ion-icon name="document-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> cleanup-old-backups.sh

# <ion-icon name="key-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Configurações
BACKUP_DIR="/backups/n8n"
RETENTION_DAYS=30
CLOUD_RETENTION_DAYS=90

echo "Iniciando limpeza de backups antigos..."

# <ion-icon name="sparkles-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Limpar backups locais
find $BACKUP_DIR -name "*.sql.gz" -mtime +$RETENTION_DAYS -delete
find $BACKUP_DIR -name "*.json.gz" -mtime +$RETENTION_DAYS -delete
find $BACKUP_DIR -name "*.tar.gz" -mtime +$RETENTION_DAYS -delete

# <ion-icon name="cloud-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Limpar backups na nuvem (S3)
aws s3 ls s3://n8n-backups/ --recursive | \
  awk '$1 < "'$(date -d "$CLOUD_RETENTION_DAYS days ago" +%Y-%m-%d)'" {print $4}' | \
  xargs -I {} aws s3 rm s3://n8n-backups/{}

echo "Limpeza concluída"
```

---

## <ion-icon name="chevron-forward-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Checklist de Backup

### Configuração

- [ ] Estratégia 3-2-1 implementada
- [ ] Scripts de backup criados
- [ ] Automação com cron configurada
- [ ] Backup na nuvem configurado
- [ ] Retenção de backups definida

### Recuperação

- [ ] Scripts de restauração criados
- [ ] Testes de restauração realizados
- [ ] Plano de DR documentado
- [ ] RTO e RPO definidos
- [ ] Procedimentos de recuperação testados

### Monitoramento

- [ ] Logs de backup configurados
- [ ] Alertas de falha configurados
- [ ] Métricas de backup coletadas
- [ ] Relatórios de backup gerados
- [ ] Verificação de integridade implementada

### Documentação

- [ ] Procedimentos documentados
- [ ] Contatos de emergência definidos
- [ ] Matriz de responsabilidades
- [ ] Plano de comunicação
- [ ] Exercícios de DR realizados

---

## <ion-icon name="arrow-forward-circle-outline" style={{ fontSize: '24px', color: '#ea4b71' }}></ion-icon> Próximos Passos

Agora que você implementou backup e recovery:

1. **[Monitoramento](./monitoring)** - Configure alertas e métricas
2. **[Autenticação](./autenticacao)** - Configure métodos de login seguros
3. **[Usuários e Permissões](./usuarios-permissoes)** - Configure controle de acesso

---

:::tip **Dica Pro**
Teste regularmente seus backups restaurando-os em ambiente de teste. Um backup que não pode ser restaurado é inútil.
:::

:::warning **Importante**
Mantenha pelo menos uma cópia dos backups fora do local principal e teste a recuperação pelo menos mensalmente.
:::

:::info **Recurso Adicional**
Considere implementar backup contínuo (continuous backup) para sistemas críticos que não podem perder dados.
:::

---

**Links úteis:**

- [Documentação oficial n8n](https://docs.n8n.io/)
- [Backup e Restore n8n](https://docs.n8n.io/hosting/backup-restore/)
- [Segurança n8n](https://docs.n8n.io/hosting/security/)
