# Documentação do DevOps de Monitoramento

## O que é monitoramento e para que serve?

Monitoramento é o processo contínuo de coleta, análise e visualização de dados de sistemas, aplicações e infraestrutura. Ele serve para:

- Identificar problemas de desempenho ou falhas.
- Prever possíveis interrupções ou gargalos.
- Acompanhar métricas importantes em tempo real.
- Garantir que os sistemas estejam funcionando conforme o esperado.
- Ajudar na resolução rápida de incidentes, minimizando impactos no usuário final.

No DevOps, o monitoramento é fundamental para a observabilidade do sistema, garantindo visibilidade e controle sobre os componentes da aplicação.

## Ferramentas Utilizadas

### Prometheus

O **Prometheus** é uma plataforma de monitoramento e alerta baseada em métricas. Ele coleta e armazena métricas numéricas de séries temporais, como o uso de CPU ou tráfego de rede.

#### Características principais:

- Baseado em *pull model* (Prometheus busca dados nos alvos configurados).
- Linguagem de consulta própria, o **PromQL**, para análises avançadas.
- Suporte a alertas configuráveis por meio de um **Alertmanager**.

### Grafana

O **Grafana** é uma ferramenta de visualização que permite criar dashboards interativos a partir de várias fontes de dados, incluindo o Prometheus.

#### Características principais:

- Dashboards personalizáveis.
- Suporte a várias fontes de dados (Prometheus, MySQL, Elasticsearch, etc.).
- Facilita a análise de métricas com gráficos e alertas visuais.

## Processo de Configuração

### Passo a Passo de Configuração

#### Configuração do Prometheus

1. **Baixar e instalar o Prometheus**:
    - Baixe a versão mais recente do Prometheus em [prometheus.io](https://prometheus.io).
    - Extraia o conteúdo para o diretório desejado.

2. **Configurar o arquivo `prometheus.yml`**:
    - No diretório do Prometheus, edite o arquivo `prometheus.yml` para incluir os alvos a serem monitorados.

    ```yaml
    global:
      scrape_interval: 15s  # Intervalo de coleta das métricas

    scrape_configs:
      - job_name: "localhost-backend"
         metrics_path: '/actuator/prometheus'
         static_configs:
          - targets: ["localhost:9090"]
    ```

3. **Testar o Prometheus**:
    - Abra o terminal no diretório do Prometheus.
    - Execute o comando:
    ```bash
    prometheus.exe --config.file=prometheus.yml --web.listen-address=:9091
    ```
    - Acesse [http://localhost:9091](http://localhost:9091) no navegador para verificar se o Prometheus está funcionando.

#### Configuração do Grafana

1. **Baixar e instalar o Grafana**:
    - Baixe o Grafana no site oficial: [grafana.com](https://grafana.com).
    - Extraia o conteúdo para o diretório desejado.

2. **Configuração do grafana**:
    - Crie um diretório no pasta do grafana _C:\caminho\para\grafana\data_   
    2.1 **Configurar o defaults.ini**
      - Vá até o arquivo defaults.ini _C:\caminho\para\grafana\conf\defaults.ini_
      - Abra o arquivo defaults.ini com um editor de texto (como o Notepad++, VSCode ou Notepad).
      - Procure pela seção chamada [paths]. Você pode fazer isso procurando a palavra paths no arquivo (geralmente ela está no início do arquivo).
      - Dentro da seção [paths], você verá a linha data = <caminho atual>. Altere o valor para o novo caminho onde você deseja armazenar os dados do Grafana. Por exemplo: _data = C:/novo/caminho/para/grafana/data_
      - Procure por _http_port=3000_ e coloque a porta do grafana. Que no caso é 3001

3. **Executar o servidor Grafana**:
    - Navegue até o diretório `bin` dentro da pasta do Grafana.
    - Execute o comando:
    ```bash
    grafana-server.exe --homepath "C:\caminho\para\grafana"
    ```
    - Acesse [http://localhost:3001](http://localhost:3001) no navegador.

4. **Adicionar o Prometheus como fonte de dados no Grafana**:
    - Faça login no Grafana (usuário: `admin`, senha padrão: `Api2024`).

### Automação com Arquivo .bat

Para simplificar a execução dos serviços, foi criado um arquivo `.bat` que inicia tanto o Prometheus quanto o Grafana.

#### Exemplo de arquivo `.bat`:

```bat
@echo off
echo Starting Prometheus...
start "" "C:\caminho\para\prometheus\prometheus.exe" --config.file="C:\caminho\para\prometheus\prometheus.yml" --web.listen-address=:9091

echo Starting Grafana...
start "" "C:\caminho\para\grafana\bin\grafana-server.exe" --homepath "C:\caminho\para\grafana"

echo All services started. Press any key to exit...
pause
```
## Endpoints Disponíveis

### Grafana
- **URL**: `http://localhost:3001`

### Prometheus
- **URL**: `http://localhost:9091`

### Métricas do Prometheus (via Actuator)
- **URL**: `http://localhost:9090/actuator/prometheus`
