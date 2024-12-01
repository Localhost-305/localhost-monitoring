
# Monitoramento com Prometheus e Grafana

Este projeto demonstra como configurar e utilizar o **Prometheus** e **Grafana** para monitorar um banco de dados MySQL, garantindo visibilidade sobre métricas de performance e saúde do sistema.

---

## O que é monitoramento?

**Monitoramento** é o processo de coletar, analisar e visualizar dados sobre o desempenho e a saúde de sistemas e aplicações. Ele ajuda a identificar problemas rapidamente, entender padrões de uso e melhorar a eficiência do sistema.

---

## O que é o Prometheus?

**Prometheus** é uma ferramenta de monitoramento e alerta de código aberto, criada para armazenar métricas em séries temporais. Ele coleta dados de sistemas e serviços configurados como alvos e oferece suporte para alertas e consultas avançadas.

### Recursos principais do Prometheus:
- Coleta de métricas com base em _pull_ (o Prometheus busca as métricas dos serviços).
- Linguagem de consulta (PromQL) para explorar e analisar os dados coletados.
- Integração com ferramentas de visualização e alerta, como Grafana e Alertmanager.

---

## O que é o Grafana?

**Grafana** é uma plataforma de análise e visualização de métricas. Ele permite criar painéis interativos e visuais para explorar dados coletados por ferramentas como Prometheus, MySQL, Elasticsearch, entre outras.

### Recursos principais do Grafana:
- Dashboards customizáveis para visualizar dados em gráficos e tabelas.
- Suporte a diversos bancos de dados e fontes de dados.
- Alertas configuráveis baseados em condições personalizadas.

---

## Configuração do projeto

### Pré-requisitos

- **Docker** e **Docker Compose** instalados no sistema.
- Banco de dados **MySQL** em execução.
- Conhecimentos básicos sobre o terminal/linha de comando.

---

### Passos para configurar e executar

#### 1. Instalar e configurar o MySQL Exporter

O **MySQL Exporter** é uma ferramenta que expõe métricas do banco de dados MySQL para que o Prometheus possa coletá-las.

1. **Crie um usuário no MySQL com permissões específicas**:
   ```sql
   CREATE USER 'exporter_user'@'%' IDENTIFIED BY 'your_password';
   GRANT PROCESS, REPLICATION CLIENT, SHOW DATABASES ON *.* TO 'exporter_user'@'%';
   FLUSH PRIVILEGES;
   ```

2. **Configure o MySQL Exporter**:
   - Baixe o MySQL Exporter para sua plataforma: [MySQL Exporter Releases](https://github.com/prometheus/mysqld_exporter/releases)
   - Configure a variável `DATA_SOURCE_NAME` com as credenciais do usuário MySQL:
     ```bash
     export DATA_SOURCE_NAME='exporter_user:your_password@(localhost:3306)/'
     ```

3. **Inicie o MySQL Exporter**:
   ```bash
   ./mysqld_exporter --web.listen-address=":9104"
   ```

4. **Verifique se as métricas estão sendo expostas**:
   - Acesse `http://localhost:9104/metrics` no navegador.

---

#### 2. Configurar o Prometheus

1. **Edite o arquivo de configuração** `prometheus.yml`:
   ```yaml
   global:
     scrape_interval: 15s

   scrape_configs:
     - job_name: "mysql"
       static_configs:
         - targets: ["localhost:9104"]  # Endereço onde o MySQL Exporter está rodando.
   ```

2. **Inicie o Prometheus**:
   - Com Docker Compose:
     ```bash
     docker-compose up prometheus
     ```
   - Ou manualmente:
     ```bash
     ./prometheus --config.file=prometheus.yml
     ```

3. **Verifique o Prometheus**:
   - Acesse `http://localhost:9090` no navegador para explorar as métricas.

---

#### 3. Configurar o Grafana

1. **Inicie o Grafana**:
   - Com Docker Compose:
     ```bash
     docker-compose up grafana
     ```
   - Ou manualmente:
     ```bash
     ./grafana-server
     ```

2. **Acesse o Grafana**:
   - URL: `http://localhost:3000`
   - Credenciais padrão: `admin` / `admin` (alterar ao logar pela primeira vez).

3. **Adicione o Prometheus como fonte de dados**:
   - No painel do Grafana, vá em **Configuration > Data Sources > Add Data Source**.
   - Escolha **Prometheus** e configure a URL como `http://localhost:9090`.

4. **Crie um painel**:
   - No menu lateral, vá em **Create > Dashboard** e adicione gráficos com consultas ao Prometheus.

---

## Exemplos de Métricas

### Métricas disponíveis do MySQL Exporter

- **`mysql_global_status_threads_connected`**: Número de threads conectadas no momento.
- **`mysql_global_status_queries`**: Total de consultas executadas.
- **`mysql_global_status_uptime`**: Tempo de atividade do servidor MySQL.

### Exemplos de consultas no Prometheus

- **Conexões ativas**:
  ```promql
  mysql_global_status_threads_connected
  ```
- **Taxa de consultas por segundo**:
  ```promql
  rate(mysql_global_status_queries[1m])
  ```

---

## Como saber se está funcionando?

1. **No Prometheus**:
   - Acesse `http://localhost:9090` e busque por métricas como `mysql_global_status_threads_connected`.

2. **No Grafana**:
   - Verifique se os gráficos mostram as métricas esperadas.
   - Crie alertas para eventos específicos (como alto número de conexões ou consultas lentas).

---


