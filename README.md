Solution for Prometheus & Grafana for Week 6 Day 2

Tasks:

1. Install Prometheus Server
- Configuration basic authentication username/password
- Screenhot of login prompt while trying to access prometheus
- Screenshot of prometheus dashboard

##### Installing Prometheus Server
![image](https://user-images.githubusercontent.com/23631617/144733918-cfd70ca0-04c8-43e8-988a-cfdf71968679.png)

##### Configuration: basic authentication username/password

We need to create a `yml config` with a bcrypt-hashed password like this:
```YAML
basic_auth_users:
    admin: $2b$12$hNf2lSsxfm0.i4a.1kVpSOVyBCfIB51VRjgBUyv6kdnyTlgWj81Ay
```

Now we can use the new yml config as a web config for Prometheus.

```shell
tom@kb1:/opt/prometheus$ sudo ln -fs `pwd`/{prometheus,promtool} /usr/bin/
tom@kb1:/opt/prometheus$ promtool check web-config web.yml 

```
![image](https://user-images.githubusercontent.com/23631617/144734716-177de95b-7422-4d41-9e52-d8268ae9a887.png)

##### Screenhot of login prompt while trying to access prometheus
![image](https://user-images.githubusercontent.com/23631617/144734907-0b03679f-b9e5-4830-9bae-bf33cc0d2d44.png)

##### Screenshot of prometheus dashboard

![image](https://user-images.githubusercontent.com/23631617/144734941-e6864851-3780-411a-9ca6-43a2c505ddfd.png)
![image](https://user-images.githubusercontent.com/23631617/144734946-bea60f59-ae8b-4873-aed2-713fa3748cbc.png)

---


2. Install node exporter on another machine than the server
- Add that machine target to server configuration
- Share screenshot from status->targets to show the available nodes
- Share configuration of node exporter & prometheus server

##### Install node exporter on another machine than the server
```shell
dimm3r@lite:~$ sudo apt install prometheus-node-exporter
dimm3r@lite:~$ prometheus-node-exporter
```

![image](https://user-images.githubusercontent.com/23631617/144735147-197e3b7b-76fb-4561-a73a-9996cd842ea3.png)

##### Share screenshot from status->targets to show the available nodes
![image](https://user-images.githubusercontent.com/23631617/144735676-a70b4777-8c79-4aea-b8bb-a177ab20b276.png)

##### Share configuration of node exporter & prometheus server

```YAML 
# prometheus.yml

global:

alerting:
  alertmanagers:
    - static_configs:
        - targets:

rule_files:

scrape_configs:
  - job_name: "prometheus"
    static_configs:
      - targets: ["localhost:9090"]
    basic_auth:
        username: assignment
        password: **********

  - job_name: "node_exporter"
    static_configs:
      - targets: ["192.168.122.116:9100"]
```

3. Install grafana server on same server as prometheus 
- Add prometheus data source to grafana, should be connected through basic auth
- Screenshot of working data source config
- Import & apply dashboard for node_exporter
- Screenshot of dashboard of nodes with live metrics shown.

Sample Server Config:
global:
  scrape_interval: 10s
  scrape_timeout: 6s

scrape_configs:
  - job_name: 'prometheus'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.10.4.105:9090']

  - job_name: 'node'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.10.5.218:9100','10.10.4.105:9100']

  - job_name: 'docker'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.10.5.218:9323']

  - job_name: 'mysql'
    scrape_interval: 5s
    static_configs:
      - targets: ['10.10.5.218:9104']

rule_files:
  - "prometheus-rules.yml"

alerting:
  alertmanagers:
  - static_configs:
    - targets: ['localhost:9093']


