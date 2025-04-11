# Redis Analyzer 🚀

Diagnóstico inteligente para ambientes Redis.  
Combina análise de performance, detecção de anomalias via Machine Learning e health check de configuração com uma engine de regras.

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

#Comentário:
A ideia inicial era fazer da Selene um algoritmo de machine learning completo, que realmente aprendesse com o uso do Redis em tempo real.
Mas eu sei que nem todo mundo tem uma instância com recursos suficientes pra isso.
Então, essa versão foi a forma que encontrei de entregar parte do potencial da Selene — de forma leve, acessível e sem exigir uma infraestrutura potente.

Selene te ajuda a detectar:

- Comandos problemáticos no `SLOWLOG` e no uso diário
- Más práticas de configuração (como `requirepass`, `noeviction`, `HGETALL` sem TTL)
- Anomalias em métricas como memória, CPU e conexões

## 📁 Estrutura do Projeto
app/  ├── main.py # Aplicação principal (Dash) 

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

## ⚙️ Componentes do Projeto

### 📊 app/main.py
Responsável por renderizar toda a interface da aplicação usando Dash.  
Conecta os painéis de análise com os módulos da Selene para mostrar:

-Métricas atualizadas a cada 10 segundos (isso pode ser configurado por você para tempo real, caso necessário, diretamente na aba de configurações da aplicação).
- Alertas técnicos
- Sugestões baseadas em ML e regras

---

### 🎨 assets/
Contém os arquivos CSS usados para estilizar o painel.  
Deixa a interface mais amigável, escura e profissional (porque ninguém merece Dash sem estilo kkk).

---

### 🧠 selene/
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

---

## 🔄 O que vem por aí

- [ ] Treinamento incremental baseado em datasets reais
- [ ] Deploy via Docker

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
