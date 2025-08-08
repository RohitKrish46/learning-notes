# Redis Overview
- **Full form:** Remote Dictionary Server
- **Storage:** Data is kept in RAM (in-memory)
- **Primary use:** Cache to improve performance
- **Data types:** Can store multiple formats and persist them

## Challenges of Using Multiple Data Services
(e.g., MySQL, Elasticsearch, Graph DB in one project)
- Need to deploy & maintain each service
- Requires expertise in all services
- Different scaling & infra needs
- Cloud-managed services can be costly (pay for each)
- In microservices: complex connectors needed for data-service communication
- Higher latency due to multiple network hops

## Redis as a Multimodal Data Service
Use one database for multiple data types (relational, graph, document, cache)

**Benefits:**
- Run & maintain only one DB
- Simpler management
- Lower latency
- No separate cache layer
- Faster application & testing (schema-less, in-memory)

## How Redis Works
- **Redis Core:** Key-value store supporting multiple data types
- **Modules:** Add-on features for specific needs (e.g., Redis Search)
- **Built-in cache:** No extra cache layer needed
- **Performance:** Super-fast due to in-memory architecture

## Persistence in Redis
**How data is kept safe:**

### Snapshotting (RDB)
- Creates point-in-time snapshots (single file)
- Good for backup & recovery
- Risk: may lose latest minutes of data

### Append Only File (AOF)
- Logs every write operation continuously
- More durable
- Can be slower than RDB

**Best practice:** Use RDB + AOF for durability.

**Storage tip:**
- Keep persisted data separate from compute (e.g., EC2 RAM for runtime + EBS for persistence)

## Cost Optimization — Redis on Flash
- RAM storage is expensive
- **Redis on Flash:**
  - Hot/frequent data → stored in RAM
  - Cold/less-used data → stored on SSD
  - Reduces infrastructure costs

## Scaling Redis

### 1. Clustering
- Primary handles reads & writes
- Replicas handle reads → better performance
- If primary fails → replica promoted to primary
- **Best practice:** Distribute replicas across multiple nodes/servers

### 2. Sharding
- Dataset split into smaller shards
- Each shard handles writes for its data subset

**Benefits:**
- Less memory needed per shard
- Horizontal scaling possible
- Can reshard as data grows

**Pro tip:** Use multiple nodes + replicas + sharding for high performance & availability.  
**Note:** Manual management unless using Redis Enterprise.

## Cross-Region Availability
**Needed for:**
- Global users → lower latency
- Disaster recovery

**Solution:** Active-Active Geo Distribution in Redis Enterprise

**Features:**
- Each region has a local cluster, synced across regions
- Independent operation if sync is interrupted
- **Conflict resolution:** CRDT (Conflict-free Replicated Data Types)
  - No data loss
  - Merges parallel changes intelligently based on data type

## Running Redis on Kubernetes
**Self-managed option:**
- Open-source Redis + Helm chart or K8s manifests
- Same replication, sharding, scaling rules
- Hosts = K8s pods (not EC2/VMs)

**Managed option:**
- Redis Enterprise on K8s via Operator
- Less operational overhead