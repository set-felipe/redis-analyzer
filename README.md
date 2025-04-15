# Redis Analyzer 🚀
> **Aviso:** Este projeto não é afiliado à Redis Ltd

Diagnóstico inteligente para ambientes Redis.  
Combina análise de performance, detecção de anomalias via Machine Learning e health check de configuração com uma engine de regras.

🔍 Tipo: Ferramenta de diagnóstico e observabilidade.

🧠 Tecnologia: Python + Dash + Redis + IA.

🚀 Objetivo: Identificar e explicar comandos ineficientes, más práticas e gargalos de configuração no Redis.

🧰 Funcionalidades: SLOWLOG Analyzer, Healthcheck, PDF Report, Recomendações de ML, Monitor de Recursos.

---
## 🧠 Quem é a Selene?

Selene é a inteligência por trás do Redis Analyzer — e também o nome da minha gata kk

Ela foi criada com uma combinação de técnicas de **AutoML**, **classificação supervisionada** e uma **engine de regras baseada em boas práticas do Redis**.

A proposta da Selene é simples:  
> “Te mostrar onde o Redis pode estar sofrendo — e como melhorar isso.”

Ela não depende de treino em tempo real:  
A Selene já vem com modelos treinados e calibrados para rodar de forma leve.  
Isso significa que **você não precisa de GPU nem de instâncias pesadas** para usá-la.  
O foco aqui é acessibilidade + inteligência aplicada ao dia a dia.

**Comentário**:
A ideia inicial era fazer da Selene um algoritmo de machine learning completo, que realmente aprendesse com o uso do Redis em tempo real.

Mas eu sei que nem todo mundo tem uma instância com recursos suficientes pra isso.
Então, essa versão foi a forma que encontrei de entregar parte do potencial da Selene — de forma leve, acessível e sem exigir uma infraestrutura potente.

Selene te ajuda a detectar:

- Comandos problemáticos no `SLOWLOG` e no uso diário
- Más práticas de configuração (como `requirepass`, `noeviction`, `HGETALL` sem TTL)
- Anomalias em métricas como memória, CPU e conexões

## 📁 Estrutura do Projeto

      ├── main.py # Aplicação principal (Dash) 

      ├── assets/ # Estilos CSS e estáticos 
      
      ├── selene/ # Engine de IA: ML + regras 
      
         ├── modelo_slowlog.pkl # Modelo treinado para comandos lentos 
         
         ├── analisador_conf.py # Engine de regras para redis.conf  
         
         ├── regras_config.json # Regras de boas práticas e alertas para o .conf
         
         ├── regras_redis.json # Regras de boas práticas e alertas para comandos do slowlog
         
         ├── redis_recomendador.py # Lógica de sugestões por comando 
         
         └── ... # Outros módulos internos da Selene
         
> A pasta `selene/` é onde a mágica acontece. Ela analisa, interpreta e sugere.

## 🔍 Visão Geral

Redis Analyzer foi criado para ajudar desenvolvedores, DevOps e analistas a identificar:

- Comandos lentos (via `SLOWLOG`)
- Más práticas de configuração (`redis.conf`)
- Gargalos de performance
- Anomalias operacionais (memória, CPU, conexões)

---

### ⚙️ Componentes do Projeto


# 🧠 Arquivo `main.py` – Aplicação Principal do Redis Analyzer

Este arquivo é o ponto central da aplicação Dash + Flask responsável por:

- Carregar os dados do Redis (INFO, SLOWLOG, CONFIG, etc)
- Exibir gráficos e tabelas de performance
- Acionar a IA (Selene) para análise inteligente
- Gerar relatórios em PDF
- Orquestrar callbacks, atualizações e interações com o usuário

---

## 📌 Principais funcionalidades do `main.py`

### 🔹 Conexão com Redis

Conexão é estabelecida com fallback seguro:

```python
redis_connection = get_redis_connection()
```

Usa `config.py` para parâmetros e tenta reconectar em caso de falha.

---

### 📉 Análise de Performance (SLOWLOG)

```python
carregar_slowlog() → gerar_dataframe() → análise ML via Selene
```

Os comandos lentos são processados e convertidos em DataFrame, depois analisados pela IA.

---

### 📊 Métricas do Redis (INFO, MEMORY, STATS)

Usa:

```python
redis_connection.info()
```

Para extrair métricas como:

- Memória usada
- Comandos executados
- Conexões ativas
- CPU e uptime

---

### 📋 Healthcheck de Configuração

Via:

```python
config = redis_connection.config_get()
resultados = analisar_configuracao(config)
```

Valida parâmetros sensíveis como:

- `save`
- `maxmemory`
- `bind`
- `lazyfree`
- `cluster-announce-ip`

Usa regras definidas em `regras_config.json`.

---

### 🤖 Integração com Selene (IA)

Usa modelo treinado para classificar comandos ineficientes:

```python
df_ia = aplicar_modelo(df)
```

Resultado é apresentado como sugestões visuais, com nível de alerta, comando e recomendação.

---

### 📤 Geração de Relatório PDF

Rota `/download-pdf` dispara a função:

```python
gerar_relatorio_pdf(resultados)
```

Gera documento com métricas, diagnóstico e configurações Redis auditadas.

---

### 🧠 Dash Layout e Callbacks

O layout inclui:

- Tabelas atualizadas via `dcc.Interval`
- Gráficos com Plotly (memória, CPU, operações)
- Splash screen
- Cartões flutuantes com insights
- Botões de ação (Healthcheck, IA)

---

### 🔁 Atualização automática

Timers (`dcc.Interval`) acionam funções como:

- Reexecução da IA em intervalos definidos
- Atualização de histórico (via CSV)

---

# ⚙️ Arquivo `config.py` – Configurações da Aplicação Redis Analyzer

Este arquivo centraliza todas as configurações da aplicação, incluindo:

- Conexão com Redis
- Parâmetros do servidor Dash
- Ajustes de coleta de dados
- Temas e comportamento do dashboard
- Intervalos de atualização e performance
- Integração com IA e controle de alertas

---

## 🔌 REDIS_CONFIG

Configurações avançadas para criação de uma conexão otimizada com Redis, incluindo:

```python
REDIS_CONFIG = {
    'host': 'debian_laboratorio',
    'port': 6379,
    'socket_timeout': 5,
    'retry_on_timeout': True,
    'max_connections': 10,
    'health_check_interval': 30,
    ...
}
```

### Destaques:
- **`socket_keepalive`**: mantém conexões persistentes ativas.
- **`health_check_interval`**: evita quedas silenciosas da conexão.
- **`socket_timeout` & `socket_connect_timeout`**: protegem contra conexões travadas.

---

## 🚀 APP_CONFIG

Configurações do servidor da aplicação Dash:

```python
APP_CONFIG = {
    'host': '0.0.0.0',
    'port': 8050,
    'debug': False,
    'title': 'Redis SLOWLOG Analyzer'
}
```

---

## 📦 DATA_CONFIG

Define os limites de coleta e caminhos para histórico e modelo de IA:

```python
DATA_CONFIG = {
    'slowlog_limit': 128,
    'max_keys': 10000,
    'prefix_len': 1,
    'history_file': 'historico_memoria.csv',
    'model_path': 'selene/modelo_slowlog.pkl'
}
```

---

## 📊 DASHBOARD_CONFIG

Controla a aparência e comportamento visual do dashboard:

```python
DASHBOARD_CONFIG = {
    'update_interval': 5000,
    'table_update_interval': 30000,
    'max_table_rows': 20,
    'theme': {
        'background_color': '#1f2c56',
        'text_color': 'white',
        ...
    }
}
```

---

## 🔄 UPDATE_CONFIG

Gerencia intervalos, cache, performance, IA e alertas. Essencial para balancear **responsividade vs. carga de processamento**.

```python
UPDATE_CONFIG = {
    'interval_rapido': 5000,
    'cache_timeout': 3600,
    'ia_min_interval': 30,
    'alert_threshold': 0.8,
    ...
}
```

### Destaques:
- **`batch_size`**: define o volume de dados processados por chunk.
- **`ia_batch_size`**: controla o volume de itens analisados por vez pela IA.
- **`alert_threshold`**: sensibilidade da detecção de anomalias.
- **`alert_cooldown`**: evita spam de alertas.

---

## 🔁 Conexão com Redis

```python
redis_pool = redis.ConnectionPool(**REDIS_CONFIG)
```

Todas as conexões Redis utilizam esse pool otimizando uso de recursos.

---

## 🛡️ Função `get_redis_connection()`

Responsável por retornar uma conexão viva com Redis, com tentativa de reconexão automática em caso de falha.

```python
def get_redis_connection():
    try:
        connection = redis.Redis(connection_pool=redis_pool)
        connection.ping()
        return connection
    ...
```

---



# 🎨 assets/
Contém os arquivos CSS usados para estilizar o painel.  
Deixa a interface mais amigável, escura e profissional (porque ninguém merece Dash sem estilo kkk).

---

# 🧠 selene/
A mente do projeto. Aqui ficam os motores de análise.

- `modelo_slowlog.pkl`  
  Modelo treinado para classificar comandos do Redis que são lentos ou ineficientes.  
  Baseado no `SLOWLOG`.

- `analisador_conf.py`  
  Engine de regras que avalia o `redis.conf` e retorna alertas de segurança, performance e boas práticas.

- `regras_config.json`  
  Arquivo com todas as boas práticas e recomendações categorizadas (ex: segurança, memória, persistência...).
-`regras_redis.json`  
  Módulo que tem as regras dos comandos comandos e sugere melhorias (ex: substituir `DEL` por `UNLINK`, evitar `KEYS *`, etc)

- `recomendador.py`  
  Módulo que interpreta comandos e sugere melhorias (ex: substituir `DEL` por `UNLINK`, evitar `KEYS *`, etc).


### 🧠 1. Módulo de Performance e ML

- Detecta comandos lentos usando modelos supervisionados
- Classifica padrões como `KEYS *`, `HGETALL`, `LRANGE`, etc.
- Sugere melhorias com base no tempo de resposta, tamanho e contexto

### 🛡️ 2. Health Check de Configuração
 (isso pode ser totalmente customisavel por você no arquivo regras_config.json)
- Engine de regras com mais de 40 boas práticas
- Analisa `requirepass`, `protected-mode`, `appendonly`, `lazyfree`, etc.
- Cada alerta vem com explicação técnica + sugestão de correção

---

## 📈 O que já está pronto

- ✔️ Diagnóstico completo do `redis.conf`
- ✔️ Regras com categorias (segurança, memória, persistência, etc.)
- ✔️ Engine leve e interpretável
- ✔️ Estrutura pronta para receber ML e Dash
- ✔️ Treinamento incremental baseado em datasets reais

---

## 🔄 O que vem por aí 

- [ ] Deploy via Docker da versão StandAlone
- [ ] Publicar uma versão de teste do projeto.
- [ ] Deploy via Docker da versão com suporter para Cluster e mais de um nó Master -> Replica

---

## 🤝 Contribua

Esse projeto está em fase inicial, mas com roadmap ativo.  
Quer testar, sugerir ou contribuir? Abre uma issue ou chama no LinkedIn 👇

---

## 📫 Contato

Feito com 🧠 e ☕ por [Felipe Feitosa]  
[LinkedIn](www.linkedin.com/in/felipe-feitosa-484700300) • [GitHub](https://github.com/set-felie) 

---

> **Aviso:** Este projeto não é afiliado à Redis Ltd e é totalmente Open-Source.
