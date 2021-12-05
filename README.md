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
    admin: [REDACTED_BCRYPT_ENCRYPTED_PASSWORD_HASH]
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
![image](https://user-images.githubusercontent.com/23631617/144736907-fab8c9b6-430b-4159-b662-3dd123e3f04b.png)

##### Share screenshot from status->targets to show the available nodes
![image](https://user-images.githubusercontent.com/23631617/144735676-a70b4777-8c79-4aea-b8bb-a177ab20b276.png)

##### Share configuration of node exporter & prometheus server

* Prometheus Server Config

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
        password: [REDACTED_PLAINTEXT_PASSWORD]

  - job_name: "node_exporter"
    static_configs:
      - targets: ["192.168.122.116:9100"]
    basic_auth:
        username: node0
        password: [REDACTED_PLAINTEXT_PASSWORD]
```

* Node Exporter Config

```YAML
basic_auth_users: 
  node0: [REDACTED_BCRYPT_ENCRYPTED_PASSWORD_HASH]
```

3. Install grafana server on same server as prometheus 
- Add prometheus data source to grafana, should be connected through basic auth
- Screenshot of working data source config
- Import & apply dashboard for node_exporter
- Screenshot of dashboard of nodes with live metrics shown.

##### Install grafana server on same server as prometheus 
![image](https://user-images.githubusercontent.com/23631617/144736310-05e69585-dd9e-41b4-a5a5-d9665a8c08a2.png)
![image](https://user-images.githubusercontent.com/23631617/144736338-6ed37b32-351b-475c-b148-1c0533f311a2.png)

##### Add prometheus data source to grafana, should be connected through basic auth
![image](https://user-images.githubusercontent.com/23631617/144737155-ef3d9a3d-af21-46f3-8ee4-ae3f5c328826.png)

##### Import & apply dashboard for node_exporter

* Using Dashboard from https://grafana.com/grafana/dashboards/1860

![image](https://user-images.githubusercontent.com/23631617/144737340-7ba96f36-618c-46d9-80ef-0753551e32e1.png)

##### Screenshot of dashboard of nodes with live metrics shown

![image](https://user-images.githubusercontent.com/23631617/144737377-1658895f-c740-4ed4-8cde-05e07c358a7e.png)
