# 📊 Dashboard Industrial de Lavanderia - Documentação Completa

## 🎯 Visão Geral

O Dashboard Industrial de Lavanderia é uma aplicação web desenvolvida em Python usando Dash/Plotly para monitoramento em tempo real de processos industriais de lavanderia. O sistema oferece análise de eficiência, consumo de água, químicos e produção.

## 🏗️ Arquitetura do Sistema

### Tecnologias Utilizadas
- **Backend**: Python 3.8+
- **Framework Web**: Dash (Plotly)
- **Banco de Dados**: PostgreSQL
- **Visualização**: Plotly
- **Processamento**: Pandas, NumPy
- **Autenticação**: Hash MD5 com JSON
- **Deploy**: VPS Ubuntu com Nginx (opcional)

### Estrutura de Arquivos
```
LavanderiaMonitor/
├── dstech_app.py              # Aplicação principal Dash
├── dstech_charts.py           # Funções de gráficos e cálculos
├── users.json                 # Dados de usuários
├── .env_dstech               # Variáveis de ambiente
├── requirements.txt          # Dependências Python
├── app/
│   └── assets/
│       ├── custom.css        # Estilos personalizados
│       └── responsive.css    # CSS responsivo moderno
├── README.md                 # Documentação básica
└── DOCUMENTACAO_COMPLETA.md  # Esta documentação
```

## 🗄️ Estrutura do Banco de Dados

### Tabelas Principais

#### 1. Rel_Diario
Dados diários de produção consolidados.
```sql
Colunas:
- Time_Stamp (timestamp): Data/hora do registro
- Time_Stamp_ms (bigint): Timestamp em milissegundos
- C0 (double): Ciclos totais
- C1 (double): Água total consumida (litros)
- C2 (double): Tempo total de operação
- C3 (double): Eficiência média
- C4 (double): Peso total produzido (kg)
- Bias (bigint): Offset de dados
```

#### 2. Rel_Quimico
Dados de consumo de químicos por processo.
```sql
Colunas:
- Time_Stamp (timestamp): Data/hora do registro
- Time_Stamp_ms (bigint): Timestamp em milissegundos
- Q1 (double): Químico 1 - Detergente principal
- Q2 (double): Químico 2 - Amaciante
- Q3 (double): Químico 3 - Alvejante
- Q4 (double): Químico 4 - Detergente secundário
- Q5 (double): Químico 5 - Neutralizante
- Q6 (double): Químico 6 - Desinfetante
- Q7 (double): Químico 7 - Reservado
- Q8 (double): Químico 8 - Aditivo especial
- Q9 (double): Químico 9 - Condicionador
- Q10 (double): Químico 10 - Reservado
- Bias (bigint): Offset de dados
```

#### 3. Rel_Carga
Dados de cargas individuais (não utilizada na versão atual).

## 📊 Cálculos e Métricas

### Fórmulas Implementadas

#### 1. Eficiência de Produção
```python
eficiencia_percent = (peso_produzido / peso_teorico) * 100
```

#### 2. Consumo de Água por Kg
```python
agua_por_kg = agua_total_litros / peso_produzido_kg
```

#### 3. Consumo de Químicos por Kg
```python
quimico_n_por_kg = chemical_n / production_weight
```
Onde:
- `chemical_n`: Valor do químico Q1 a Q9 da tabela Rel_Quimico
- `production_weight`: Peso produzido (C4) da tabela Rel_Diario

#### 4. Ciclos por Hora
```python
ciclos_por_hora = total_ciclos / horas_operacao
```

#### 5. Média de Tempo por Ciclo
```python
tempo_medio_ciclo = tempo_total_operacao / total_ciclos
```

### Sincronização de Dados

#### Processo de JOIN entre Tabelas
```sql
-- Exemplo de consulta para químicos
SELECT 
    rq."Time_Stamp" as timestamp,
    rq."Q1" as chemical_1,
    rq."Q2" as chemical_2,
    -- ... outros químicos
    rd."C4" as production_weight
FROM "Rel_Quimico" rq
LEFT JOIN "Rel_Diario" rd ON DATE(rq."Time_Stamp") = DATE(rd."Time_Stamp")
ORDER BY rq."Time_Stamp" DESC
```

#### Tratamento de Dados Ausentes
- **Valores NULL**: Substituídos por 0 ou valor padrão
- **Divisão por Zero**: Peso mínimo de 1kg para evitar erros
- **Datas sem Correspondência**: LEFT JOIN mantém dados de químicos mesmo sem produção

## 🔧 Configuração e Deploy

### Variáveis de Ambiente (.env_dstech)
```bash
DB_HOST=localhost
DB_NAME=dstech_dashboard
POSTGRES_USERNAME=postgres
POSTGRES_PASSWORD=postgres123
DASH_DEBUG=True
```

### Instalação Local
```bash
# 1. Clonar repositório
git clone https://github.com/dennisiautomation/dstechdashquimicos
cd dstechdashquimicos

# 2. Criar ambiente virtual
python -m venv venv
source venv/bin/activate  # Linux/Mac
# ou
venv\Scripts\activate     # Windows

# 3. Instalar dependências
pip install -r requirements.txt

# 4. Configurar banco de dados
# Editar .env_dstech com suas credenciais

# 5. Executar aplicação
python dstech_app.py
```

### Deploy em VPS
```bash
# 1. Conectar ao servidor
ssh root@195.35.19.46

# 2. Instalar dependências do sistema
apt update && apt install python3 python3-pip python3-venv postgresql-client

# 3. Criar ambiente virtual
python3 -m venv venv
source venv/bin/activate

# 4. Instalar dependências Python
pip install -r requirements.txt

# 5. Configurar variáveis de ambiente
nano .env_dstech

# 6. Executar aplicação
python dstech_app.py
```

## 🎨 Interface e UX

### Funcionalidades Principais

#### 1. Sistema de Login
- Autenticação via hash MD5
- Sessão persistente
- Logout seguro

#### 2. Dashboard Principal
- **Resumo**: Métricas principais do dia
- **Tendências**: Gráficos de evolução temporal
- **Alarmes**: Monitoramento de alertas
- **Produção**: Análise detalhada de produção
- **Relatórios**: Exportação de dados
- **Configurações**: Ajustes do sistema

#### 3. Modo Escuro
- Toggle entre tema claro/escuro
- Persistência da preferência
- Adaptação automática de cores

#### 4. Responsividade
- Design mobile-first
- Breakpoints otimizados
- Navegação touch-friendly

### Componentes Visuais

#### Cards de Métricas
```python
dbc.Card([
    dbc.CardBody([
        html.H2(id="metric-value", className="metric-value"),
        html.P("Descrição", className="metric-label")
    ])
], className="metric-card")
```

#### Gráficos Interativos
- **Linha**: Tendências temporais
- **Barra**: Comparações categóricas
- **Gauge**: Indicadores de performance
- **Scatter**: Correlações

## 🔍 Monitoramento e Logs

### Logs de Sistema
```python
import logging

logging.basicConfig(
    level=logging.INFO,
    format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
    handlers=[
        logging.FileHandler('dashboard.log'),
        logging.StreamHandler()
    ]
)
```

### Métricas de Performance
- Tempo de resposta das queries
- Uso de memória
- Conexões de banco ativas
- Erros de callback

## 🚀 Otimizações Implementadas

### Performance
1. **Cache de Dados**: Armazenamento temporário de consultas frequentes
2. **Lazy Loading**: Carregamento sob demanda de gráficos
3. **Debounce**: Redução de chamadas desnecessárias
4. **Pagination**: Limitação de registros por consulta

### Segurança
1. **Hash de Senhas**: MD5 para autenticação
2. **Sanitização**: Limpeza de inputs SQL
3. **HTTPS**: Comunicação criptografada (recomendado)
4. **Rate Limiting**: Controle de requisições

## 🐛 Troubleshooting

### Problemas Comuns

#### 1. Erro de Conexão com Banco
```bash
# Verificar conectividade
psql -h localhost -U postgres -d dstech_dashboard

# Verificar variáveis de ambiente
cat .env_dstech
```

#### 2. Callback Errors
```python
# Verificar IDs existentes no layout
print([component.id for component in layout.children if hasattr(component, 'id')])
```

#### 3. CSS não Carregando
```bash
# Verificar estrutura de assets
ls -la app/assets/

# Limpar cache do navegador
Ctrl+F5 ou Cmd+Shift+R
```

#### 4. Dados Não Aparecem
```sql
-- Verificar dados nas tabelas
SELECT COUNT(*) FROM "Rel_Diario";
SELECT COUNT(*) FROM "Rel_Quimico";

-- Verificar últimos registros
SELECT * FROM "Rel_Diario" ORDER BY "Time_Stamp" DESC LIMIT 5;
```

## 📈 Roadmap e Melhorias Futuras

### Versão 2.0
- [ ] API REST para integração externa
- [ ] Alertas por email/SMS
- [ ] Relatórios PDF automatizados
- [ ] Dashboard mobile nativo
- [ ] Machine Learning para predições

### Versão 2.1
- [ ] Multi-tenancy (múltiplas lavanderias)
- [ ] Integração com ERP
- [ ] Backup automático
- [ ] Monitoramento de equipamentos IoT

## 🤝 Contribuição

### Como Contribuir
1. Fork do repositório
2. Criar branch para feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit das mudanças (`git commit -am 'Adiciona nova funcionalidade'`)
4. Push para branch (`git push origin feature/nova-funcionalidade`)
5. Criar Pull Request

### Padrões de Código
- PEP 8 para Python
- Comentários em português
- Testes unitários obrigatórios
- Documentação atualizada

## 📞 Suporte

### Contatos
- **Desenvolvedor**: Dennis Canteli
- **Email**: dennis@automation.com
- **GitHub**: https://github.com/dennisiautomation
- **Repositório**: https://github.com/dennisiautomation/dstechdashquimicos

### Logs de Versão
- **v1.0.0**: Versão inicial com funcionalidades básicas
- **v1.1.0**: Correção de químicos e responsividade
- **v1.2.0**: Modo escuro e otimizações de performance

---

**Última atualização**: 06/07/2025
**Versão da documentação**: 1.2.0
