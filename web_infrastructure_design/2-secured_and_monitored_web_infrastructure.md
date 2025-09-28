# Secured and monitored web infrastructure

A secured and monitored web infrastructure means your website is both protected and constantly being watched to make sure it’s working properly. "Secured" means you’ve got things like firewalls to block bad traffic, HTTPS to encrypt user data, and access rules to control who can reach what. "Monitored" means you’re tracking how your servers and apps are doing. It’s all about keeping things safe, fast, and reliable.

# Design infrastructure
```mermaid
%%{init: {"flowchart": {"htmlLabels": false}}}%%
flowchart LR
  %% Internet to Edge Firewall
  A["User Browser"] -->|"HTTPS (443)"| FW1["⛨ Edge Firewall"]
  
  %% Edge Firewall to Load Balancer
  FW1 -->|"HTTPS (443)\nSSL Cert Installed"| LB["🔀 HAProxy Load Balancer"]
  
  %% Load Balancer to Internal Firewall
  LB --> FW2["⛨ Internal Firewall"]
  
  %% Internal Firewall to App Servers
  FW2 --> S1["App Server 1\nNginx + App Runtime"]
  FW2 --> S2["App Server 2\nNginx + App Runtime"]
  
  %% Each App Server to Database Firewall
  S1 --> FW3["⛨ DB Firewall"]
  S2 --> FW3
  
  %% DB Firewall to Database Nodes
  FW3 --> DB1["MySQL Primary"]
  FW3 --> DB2["MySQL Replica"]
  
  %% Replication Link
  DB1 -.->|"Async Replication"| DB2
  
  %% Monitoring Agents
  subgraph Monitoring
    M1["Agent on LB"]
    M2["Agent on App Server 1"]
    M3["Agent on App Server 2"]
  end
  
  LB --> M1
  S1 --> M2
  S2 --> M3
```

# Infrastructure specifics

### Firewalls 
They protect each layer by restricting unnecessary traffic and they reduce the attack surface and help prevent unauthorized access.

### HTTPS 
It encrypts all traffic between users and the load balancer so attackers can't intercept or tamper with it.HTTPS protects against eavesdropping, MITM (man-in-the-middle) attacks, and data leakage.

### What monitoring is used for

Monitoring is used to keep an eye on your servers and applications it makes sure everything’s running smoothly. It helps you spot problems early, like high CPU usage, server crashes, or if your database is too slow. Basically, it gives you visibility into how your infrastructure is performing and alerts you when something’s off.

### Data collection

Each server runs a small monitoring agent in the background. This agent collects system metrics like CPU, memory, disk usage, and also logs or app-specific stats. It then sends all this data to a central monitoring platform, where you can view dashboards, set alerts, and analyze trends.

### QPS monitoring 

To track QPS, you’d either enable access logs in your web server. The monitoring agent can read these logs or metrics and calculate how many requests your server is handling per second. You can then visualize this on a dashboard and set up alerts if traffic suddenly spikes or drops

###


