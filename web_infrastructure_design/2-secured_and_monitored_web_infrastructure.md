# Secured and monitored web infrastructure

# Design infrastructure
```mermaid
%%{init: {"flowchart": {"htmlLabels": false}}}%%
flowchart TD
  subgraph Internet
    User[User Browser]
  end

  subgraph DNS
    DNS["DNS<br>www.foobar.com → LB IP"]
  end

  subgraph Firewall1["Firewall #1 (Edge)"]
    FW1[Allows ports 80/443 only]
  end

  subgraph LoadBalancer["Load Balancer (HAProxy/Nginx)"]
    LB["HAProxy<br>SSL Termination<br>HTTPS (443)"]
    MonitorLB["Monitoring Agent"]
  end

  subgraph AppServer1["App Server 1"]
    FW2["Firewall #2 (App1)"]
    Nginx1["Nginx"]
    App1["App Code"]
    DB1["MySQL (Primary)"]
    Monitor1["Monitoring Agent"]
  end

  subgraph AppServer2["App Server 2"]
    FW3["Firewall #3 (App2)"]
    Nginx2["Nginx"]
    App2["App Code"]
    DB2["MySQL (Replica)"]
    Monitor2["Monitoring Agent"]
  end

  User -->|1. Request https://www.foobar.com| DNS
  DNS -->|2. DNS Resolution| FW1 --> LB
  LB -->|3. Forward Request| FW2 --> Nginx1 --> App1
  LB -->|4. Forward Request| FW3 --> Nginx2 --> App2
  App1 -->|5. DB Query| DB1
  App2 -->|6. DB Query| DB2
  DB1 -->|7. Replication| DB2

  MonitorLB -->|Monitoring| LB
  Monitor1 -->|Monitoring| App1
  Monitor2 -->|Monitoring| App2
```

# Infrastructure specifics
