# 📊 MongoDB vs DynamoDB

| Feature               | MongoDB                                                              | DynamoDB                                                                 |
|-----------------------|----------------------------------------------------------------------|--------------------------------------------------------------------------|
| **Type**              | Document-oriented NoSQL DB                                           | Fully managed NoSQL key-value and document DB                            |
| **Vendor**            | MongoDB Inc.                                                         | Amazon Web Services (AWS)                                                |
| **Data Model**        | BSON (Binary JSON) documents                                         | JSON-like documents with key-value access                                |
| **Schema**            | Flexible, dynamic schema                                             | Flexible schema, limited data types and nesting                          |
| **Hosting**           | Self-hosted / MongoDB Atlas (Cloud)                                  | Fully managed on AWS                                                     |
| **Scaling**           | Manual (sharding, replica sets)                                      | Automatic horizontal scaling                                             |
| **Consistency**       | Tunable (eventual/strong via write concern)                         | Eventually consistent (strong optional per request)                      |
| **Transactions**      | Multi-document ACID transactions (since v4.0)                        | ACID (mostly within the same partition key)                              |
| **Indexing**          | Rich indexing (compound, geospatial, text, wildcard)                | Simple and global secondary indexes only                                 |
| **Query Language**    | Rich queries, aggregations, map-reduce                              | Limited filter/query API                                                 |
| **Backup & Restore**  | Snapshot/point-in-time backups (Atlas)                              | Point-in-time restore and backup tools available                         |
| **Performance**       | Config-dependent latency and performance                           | High performance, low-latency at massive scale                           |
| **Pricing**           | Depends on infra / Atlas pricing model                             | Pay-per-use (RCU/WCU/Storage/Backup)                                     |
| **Java Integration**  | Spring Data MongoDB, native Java Driver                             | AWS SDK (DynamoDBMapper, Enhanced Client), Spring Data DynamoDB (community) |
| **Best Use Cases**    | Analytics-heavy apps, catalog, flexible schema, nested data         | Serverless APIs, real-time apps, predictable workloads                   |

---

# 🗃️ Types of NoSQL Databases

| Type              | Description                                                            | Examples                                                  | Use Cases                                   |
|-------------------|------------------------------------------------------------------------|-----------------------------------------------------------|---------------------------------------------|
| **Document**       | Stores documents (JSON/BSON)                                           | MongoDB, CouchDB, Amazon DocumentDB                       | CMS, product catalog, analytics             |
| **Key-Value**      | Simple key-value store                                                | DynamoDB, Redis, Riak, Aerospike                          | Session storage, caching, shopping carts    |
| **Column Store**   | Stores data in columns                                                | Apache Cassandra, HBase, ScyllaDB                         | IoT, logs, time-series, real-time analytics |
| **Graph Store**    | Stores nodes + relationships (edges)                                  | Neo4j, Amazon Neptune, ArangoDB, OrientDB                 | Social networks, recommendations            |
| **Search Engine**  | Indexes and searches large amounts of data                           | Elasticsearch, Solr                                       | Full-text search, log analysis              |
| **Time-Series**    | Optimized for time-stamped data                                       | InfluxDB, TimescaleDB, Prometheus                         | Metrics, monitoring, telemetry              |
| **Multi-Model**    | Combines multiple models (e.g., doc + graph)                         | ArangoDB, OrientDB, Cosmos DB                             | Complex applications with mixed data types  |
| **Object Store**   | Binary storage with metadata                                          | Amazon S3, MinIO, OpenStack Swift                         | Images, videos, backups                     |

---

# ✅ Popular NoSQL Databases and When to Use

| Database             | Type            | Best For                                                    |
|----------------------|------------------|--------------------------------------------------------------|
| **MongoDB**           | Document         | Flexible schema, nested documents, general purpose           |
| **DynamoDB**          | Key-Value        | Serverless apps, real-time APIs                              |
| **Redis**             | Key-Value        | Caching, pub-sub, leaderboards, in-memory compute            |
| **Cassandra**         | Column           | High-write throughput, time-series, distributed workloads    |
| **Neo4j**             | Graph            | Social graphs, fraud detection, complex relationships         |
| **Elasticsearch**     | Search Engine    | Full-text search, log analytics                              |
| **Couchbase**         | Document + KV    | Mobile offline-first apps, scalable caching                  |
| **InfluxDB**          | Time-Series      | Metrics, sensors, performance tracking                       |
| **ArangoDB**          | Multi-Model      | Combined graph + document use cases                          |
| **Amazon DocumentDB** | Document         | MongoDB-compatible managed service                           |
| **Cosmos DB**         | Multi-Model      | Global-scale distributed systems                             |

---

# 🧠 How to Choose?

- Need flexible schema + queries? → **MongoDB**
- Auto-scalable serverless API? → **DynamoDB**
- Fastest in-memory cache? → **Redis**
- Massive write volumes, time-series? → **Cassandra**
- Deep relationship traversal? → **Neo4j**
- Search + analytics? → **Elasticsearch**
- Mixed data model (doc + graph)? → **ArangoDB**
