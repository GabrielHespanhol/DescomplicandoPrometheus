# Manual de instalação do Prometheus

Manual de instalação do prometheus para máquinas linux. O manual está sendo criado para estudo do curso Descomplicando o prometheus da Linuxtips.

* Acesso ao site da linuxtips: [Site da Linuxtips](https://www.linuxtips.io/)
* Acesso ao site e documentação do prometheus: [Site do prometheus](https://prometheus.io)
* Acesso ao repositório do curso Descomplicando o Prometheus do github [Click aqui](https://github.com/badtuxx/DescomplicandoPrometheus)



### Download e instalação do prometheus

OBS: Para a instalação do prometheus utilizei o debian 10 como distribuição. O manual e os passos devem funcionar para outras distribuições com uso do systemD.

Primeiro vamos criar um usuário para executar o serviço do prometheus.
```
sudo addgroup --system prometheus
sudo adduser --shell /sbin/nologin --system --group prometheus
```

Realizando o download do prometheus 2.38.0.
```
curl -LO https://github.com/prometheus/prometheus/releases/download/v2.38.0/prometheus-2.38.0.linux-amd64.tar.gz
```

Descompactando os arquivos baixados
```
tar -zxf prometheus-2.38.0.linux-amd64.tar.gz
```

Movendo os binários para o diretório correto.
```
mv prometheus-2.38.0.linux-amd64/prometheus /usr/local/bin/prometheus
mv prometheus-2.38.0.linux-amd64/promtool /usr/local/bin/promtool
```

Criando o diretório de configurações do prometheus e adicionando os arquivos de configuração.
```
sudo mkdir /etc/prometheus

sudo mv prometheus-2.38.0.linux-amd64/prometheus.yml /etc/prometheus/prometheus.yml
sudo mv prometheus-2.38.0.linux-amd64/consoles /etc/prometheus
sudo mv prometheus-2.38.0.linux-amd64/console_libraries /etc/prometheus
```

Editando o arquivo de configuração prometheus.yml
```
sudo vim /etc/prometheus/prometheus.yml
```

Adicionar o seguinte conteudo:
```
global:
  scrape_interval: 15s
  evaluation_interval: 15s
  scrape_timeout: 10s
  
rule_files:

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
```

Criando diretórios restantes utilizados pelo serviço do prometheus e criando o service unit.
```
sudo mkdir /var/lib/prometheus

sudo vim /etc/systemd/system/prometheus.service
```

Conteudo do service unite:
```
[Unit]
Description=Prometheus
Documentation=https://prometheus.io/docs/introduction/overview/
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
User=prometheus
Group=prometheus
ExecReload=/bin/kill -HUP \$MAINPID
ExecStart=/usr/local/bin/prometheus \
  --config.file=/etc/prometheus/prometheus.yml \
  --storage.tsdb.path=/var/lib/prometheus \
  --web.console.templates=/etc/prometheus/consoles \
  --web.console.libraries=/etc/prometheus/console_libraries \
  --web.listen-address=0.0.0.0:9090 \
  --web.external-url=

SyslogIdentifier=prometheus
Restart=always

[Install]
WantedBy=multi-user.target
```

Alterando o proprietáro dos arquivos e diretórios para os arquivos e diretórios utilizados pelo prometheus.
```
sudo chown -R prometheus:prometheus /var/log/prometheus
sudo chown -R prometheus:prometheus /etc/prometheus
sudo chown -R prometheus:prometheus /var/lib/prometheus
sudo chown -R prometheus:prometheus /usr/local/bin/prometheus
sudo chown -R prometheus:prometheus /usr/local/bin/promtool
```

Realizando o reload e start do serviço do prometheus.

```
sudo systemctl daemon-reload

sudo systemctl start prometheus
```

Iniciar o prometheus no boot do sistema.
```
sudo systemctl enable prometheus
```

Caso tenha ocorrido tudo certo o acesso a interface web do prometheus pode ser acessado via browser.
```
# Acesso via localhost caso tenha instalado em sua máquina
http://localhost:9090

# Caso seja um servidor remoto
http://IP_SERVIDOR:9090
```