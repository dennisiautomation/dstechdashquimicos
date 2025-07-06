# 🧼 Dashboard Industrial de Lavanderia

Dashboard web para monitoramento de consumo químico e eficiência operacional de lavanderias industriais.

## 🚀 Instalação Rápida

### 1. Clonar o repositório
```bash
git clone https://github.com/dennisiautomation/dstechdashquimicos.git
cd dstechdashquimicos
```

### 2. Instalar dependências
```bash
pip install -r requirements.txt
```

### 3. Configurar banco de dados
Crie o arquivo `.env_dstech` com suas credenciais:
```
DB_HOST=seu_host
DB_NAME=seu_banco
DB_USER=seu_usuario
DB_PASSWORD=sua_senha
DB_PORT=5432
DEBUG=True
```

### 4. Executar o dashboard
```bash
python dstech_app.py
```

Acesse: `http://localhost:8051`

**Login padrão:** `admin` / `admin123`

## 📊 Funcionalidades

- **Consumo Químico**: Monitoramento de 9 tipos de químicos por kg produzido
- **Eficiência Operacional**: Cálculos de performance da produção
- **Interface Responsiva**: Funciona em desktop e mobile
- **Modo Escuro**: Toggle para melhor visualização
- **Gráficos Interativos**: Plotly com hover e zoom

## 🗄️ Estrutura do Banco

- **Rel_Quimico**: Dados de consumo químico (Q1-Q9)
- **Rel_Diario**: Dados de produção diária (peso, eficiência)
- **Rel_Carga**: Informações de cargas processadas
| client_id  | Identificador do cliente                       |
| weight     | Peso da carga processada                       |

### Tabela: `relatorio_diario`
| Coluna            | Descrição                                |
|-------------------|-------------------------------------------|
| date_time         | Data e hora do registro                   |
| downtime          | Tempo em que a máquina ficou parada       |
| production_time   | Tempo total de produção                   |
| water_consumption | Consumo de água (m³)                      |
| efficiency        | Eficiência da máquina (%)                 |
| production_weight | Quilos produzidos                         |

### Tabela: `relatorio_quimicos`
| Coluna     | Descrição                                  |
|------------|---------------------------------------------|
| date_time  | Data e hora do registro                     |
| chemical_1 a chemical_9 | Quantidade consumida de cada químico |

### Tabela: `clientes`
| Coluna      | Descrição              |
|-------------|------------------------|
| client_id   | Código do cliente      |
| client_name | Nome do cliente        |

### Tabela: `programas`
| Coluna       | Descrição              |
|--------------|------------------------|
| program_id   | Código do programa     |
| program_name | Nome do programa       |

## 🔗 Cruzamentos e Fórmulas de Indicadores

### 1. Eficiência Operacional
```python
eficiencia = (production_time / (production_time + downtime)) * 100
```
> Mede o aproveitamento produtivo da máquina.

### 2. Consumo de Água por Quilo
```python
consumo_agua_por_kg = (water_consumption * 1000) / production_weight
```
> Avalia o uso de água (em litros) por quilo de roupa lavada.

### 3. Consumo de Químicos por Quilo
```python
quimico_n_por_kg = chemical_n / production_weight
```
> Controla o uso de químicos individualmente por quilo de produção.

### 4. Alarmes Mais Frequentes
- Agrupar por `tag`
- Somar `active_time` e contar ocorrências
- Exibir Top 10 por período

### 5. Produção por Cliente / Programa
- Agrupar `relatorio_carga` por `client_id` e `program_id`
- Somar `weight`

### 6. Eficiência de Tempo
```python
eficiencia = (tempo_producao / 1440) * 100  # onde 1440 = minutos por dia
```
> Mede o percentual do tempo produtivo da máquina.

### 7. Água por Quilo de Roupa Lavada
```python
agua_por_quilo = volume_agua / quilos_roupa
```
> Indica quantos litros/m³ de água foram usados por quilo processado.

### 8. Químicos por Quilo
```python
quimico_por_kg = quantidade_quimico / producao_quilos
```
> Aplica-se para todos os químicos (Q1 a Q9), permitindo controle detalhado do uso.

## 📊 Relatórios e Dashboards
- **Filtros**: por período, cliente, programa, área
- **Relatórios disponíveis**:
  - Produção diária e por carga
  - Eficiência operacional
  - Consumo de água
  - Consumo de químicos
  - Alarmes mais frequentes
- **Exportação**: CSV, Excel e PDF

## 🌐 Acesso Remoto e Infraestrutura
- Inicialmente local (Mac)
- Posteriormente em PC com Windows, com banco acessível por IP fixo/DDNS
- Painel e API acessíveis remotamente
- Autenticação básica ou token

## 🔁 Sincronização Futura
- Agendamento para copiar dados do SQL Server para PostgreSQL
- Suporte a múltiplas plantas/clientes no futuro

## 📈 Cruzamentos Importantes
- 🔁 `relatorio_quimicos` × `relatorio_diario`: Para consumo de químicos por produção.
- 📊 `alarme_historico`: Frequência de tags e tempo ativo por alarme.
- 📦 `relatorio_carga` × `clientes` e `programas`: Análise de produção por cliente e tipo de programa.
- 📉 `relatorio_diario`: Avaliação de eficiência, consumo de água e produtividade.

### Cruzamentos Específicos
- **Alarmes**: Contagem de ocorrências por `tag` e Top 10 alarmes mais frequentes por período
- **Relatório de Carga**: Cruzamento com nome de programas (via tabela `programas`) e nome de clientes (via tabela `clientes`)
- **Relatório Diário**: Cálculo de consumo de água por quilo processado e base para cruzamento com `relatorio_quimicos`
- **Relatório de Químicos**: Cruzamento de `Q1` a `Q9` com produção em quilos da tabela `relatorio_diario` para cálculo de consumo por quilo

## 🛠️ Instalação e Configuração

### Pré-requisitos

- Python 3.8+
- SQL Server (com backup `Super_Lavagem_DB.bak 2`)
- PostgreSQL 12+
- Git

### 1. Clone o Repositório

```bash
git clone <repository-url>
cd LavanderiaMonitor
```

### 2. Instale as Dependências

```bash
pip install -r requirements.txt
```

### 3. Configure as Variáveis de Ambiente

Copie o arquivo `.env` e configure suas credenciais:

```bash
cp .env.example .env
```

Edite o arquivo `.env` com suas configurações:

```env
# Banco SQL Server
SQLSERVER_HOST=localhost
SQLSERVER_DATABASE=LavanderiaDB
SQLSERVER_USERNAME=sa
SQLSERVER_PASSWORD=YourPassword123

# Banco PostgreSQL
POSTGRES_HOST=localhost
POSTGRES_DATABASE=lavanderia_monitor
POSTGRES_USERNAME=postgres
POSTGRES_PASSWORD=YourPassword123

# Configurações da API
API_HOST=0.0.0.0
API_PORT=8000
SECRET_KEY=your-secret-key-here
ACCESS_TOKEN_EXPIRE_MINUTES=30

# Configurações do Dashboard
DASHBOARD_HOST=0.0.0.0
DASHBOARD_PORT=8050
DASHBOARD_DEBUG=False
```

### 4. Migração de Dados

#### Opção A: Com Backup do SQL Server

1. Coloque o arquivo `Super_Lavagem_DB.bak 2` na pasta do projeto
2. Renomeie para `Super_Lavagem_DB.bak`
3. Execute a migração:

```bash
python migrate_data.py
```

#### Opção B: Apenas Sincronização

Se já tem o SQL Server configurado:

```bash
python migrate_data.py
```

### 5. Criar Usuários Iniciais

```bash
python init_admin.py
```

Este script criará:
- **admin** / admin123 (Administrador)
- Usuários de demonstração (opcional)

## 🚀 Executando o Sistema

### Opção 1: Sistema Completo (Recomendado)

```bash
python start_system.py
```

Este comando inicia:
- Backend API (porta 8000)
- Dashboard Web (porta 8050)

### Opção 2: Componentes Separados

#### Backend API:
```bash
python run_api.py
```

#### Dashboard:
```bash
python run_dashboard.py
```

## 🌐 Acessando o Sistema

1. **Dashboard Principal**: http://localhost:8050
2. **API Documentation**: http://localhost:8000/docs
3. **API Redoc**: http://localhost:8000/redoc

### Login Padrão

- **Usuário**: admin
- **Senha**: admin123

⚠️ **IMPORTANTE**: Altere a senha padrão após o primeiro login!

## 📊 Funcionalidades do Dashboard

### Filtros Disponíveis
- **Período**: Seleção de data início/fim
- **Cliente**: Filtro por cliente específico
- **Programa**: Filtro por programa de lavagem

### Abas de Análise
1. **Resumo Diário**: Métricas principais e KPIs
2. **Eficiência**: Gráficos de eficiência operacional
3. **Consumo de Água**: Análise do consumo hídrico
4. **Consumo de Químicos**: Monitoramento de produtos químicos
5. **Produção por Cliente**: Análise por cliente
6. **Produção por Programa**: Análise por programa
7. **Top Alarmes**: Alarmes mais frequentes

### Exportação de Relatórios
- **Formatos**: CSV, Excel, PDF
- **Tipos**: Todos os relatórios disponíveis
- **Filtros**: Aplicam-se aos relatórios exportados

## 🔧 Configurações Avançadas

### Sincronização Automática

O sistema sincroniza dados automaticamente a cada 60 minutos (configurável).

Para alterar o intervalo:
```env
SYNC_INTERVAL_MINUTES=30
```

### Acesso Remoto

Para permitir acesso remoto, configure:
```env
REMOTE_ACCESS=true
API_HOST=0.0.0.0
DASHBOARD_HOST=0.0.0.0
```

## 🐛 Solução de Problemas

### Erro de Conexão com Banco
1. Verifique as credenciais no arquivo `.env`
2. Confirme se os bancos estão rodando
3. Teste a conectividade

### Erro de Migração
1. Verifique se o arquivo de backup existe
2. Confirme permissões do SQL Server
3. Execute `migrate_data.py` novamente

### Dashboard não Carrega
1. Verifique se a API está rodando (porta 8000)
2. Confirme se há dados no PostgreSQL
3. Verifique logs no terminal

## 📝 Logs

Logs são salvos em:
- `logs/api.log` - Logs da API
- `logs/sync.log` - Logs de sincronização
- `logs/dashboard.log` - Logs do dashboard

## 🤝 Suporte

Para suporte técnico:
1. Verifique os logs
2. Consulte a documentação da API
3. Entre em contato com a equipe de desenvolvimento

---

**Sistema de Monitoramento de Lavanderias Industriais © 2025**
