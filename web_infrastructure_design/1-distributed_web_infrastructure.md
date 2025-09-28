# Distributed web infrastructure


# Design infrastructure
```mermaid
%%{init:{"flowchart":{"htmlLabels":false}}}%%
flowchart LR
    subgraph Internet
        A[User Browser]
    end

    subgraph DNS
        B[www.foobar.com<br>A record to 8.8.8.8]
    end

    subgraph LoadBalancer["HAProxy (Load Balancer)"]
        LB[8.8.8.8<br>HAProxy<br>Round Robin]
    end

    subgraph Server1["App Server 1"]
        W1["Nginx"]
        A1["Application Server"]
        C1["Application Files"]
        DB1["MySQL Primary"]
    end

    subgraph Server2["App Server 2"]
        W2["Nginx"]
        A2["Application Server"]
        C2["Application Files"]
        DB2["MySQL Replica"]
    end

    A -- "1. DNS Lookup" --> B
    B -- "2. IP 8.8.8.8" --> A
    A -- "3. HTTP Request" --> LB
    LB -- "4a. Forwarded Request" --> W1
    LB -- "4b. Forwarded Request" --> W2
    W1 --> A1
    A1 --> C1
    A1 --> DB1
    DB1 --> A1
    W2 --> A2
    A2 --> C2
    A2 --> DB2
    DB2 --> A2
```
# Infrastructure specifics

### Load Balancer (HAProxy)

 It spreads traffic across both servers so one doesn’t get overwhelmed. This boosts availability and keeps things running smoothly.

### Two Application Servers

Redundancy and scalability. If one server goes down or needs maintenance, the other can still serve users. More servers = more capacity.

### Primary-Replica
Separates write traffic (to the Primary) from read-only queries (to the Replica). This takes pressure off the Primary DB and improves performance for users reading data.


# Load Balancer Details
**The Round-Robin Algorithm**

HAProxy sends requests to servers one by one. It assumes the servers are pretty equal in performance but you can tweak it with weights if needed.

**Active-Active vs Active-Passive**

Active-Active: Both servers are working at the same time. If one fails, the other keeps going. You lose some capacity but stay online.

Active-Passive: One server is live, and the other just waits in the background. It kicks in only if the active one fails.

# MySQL Primary-Replica Cluster
The Primary database handles write transactions, commits, and logs changes

The Replica stays in sync by copying changes from the Primary’s logs. It’s usually slightly behind since replication is asynchronous

# Infrastructure issues

### Single Points of Failure (SPOF)
HAProxy only one load balancer and if it goes down, the whole site will be  offline.

Primary DB: Only one write-capable database and if it fails, no one can update or insert data, and replication breaks.

### Security
It has no Firewall which means ll ports might be open to the internet. That's a pretty big risk.

It has no HTTPS which means Traffic is sent in plain text, attackers could snoop or intercept data.

### Monitoring
It doesn't contain health checks beyond basic TCP checks from HAProxy nor visibility into CPU, memory usage, or slow queries also centralized logs, so debugging will be harder when something breaks.
