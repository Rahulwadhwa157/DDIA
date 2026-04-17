# Distributed Systems & Data Formats -- Notes

## 1. Serialization Formats

### Protocol Buffers (ProtoBuf)

-   Binary serialization format
-   Requires `.proto` schema and code generation
-   Encodes data as: field_number + wire_type + value
-   Does NOT send schema at runtime
-   Efficient, compact, fast
-   Best for RPC (e.g., gRPC)

### Apache Avro

-   Binary format with runtime schema
-   Uses **writer schema + reader schema**
-   No field numbers stored → smaller payload
-   Strong schema evolution support
-   Common in Kafka + data pipelines

### BSON

-   Binary JSON used by MongoDB
-   Self-describing (stores field names + types)
-   Larger than Avro/ProtoBuf
-   Good for storage, not ideal for network

------------------------------------------------------------------------

## 2. Message Brokers

### What is a Message Broker?

-   Middleware that enables communication between services via messages

``` text
Producer → Broker → Consumer
```

### Popular Systems

-   Apache Kafka
-   RabbitMQ
-   ActiveMQ

------------------------------------------------------------------------

### Key Concepts

#### 1. Decoupling

-   Producers and consumers don't need to know each other

#### 2. Asynchronous Communication

-   No waiting for response
-   Improves scalability

#### 3. Durability

-   Messages can be persisted

#### 4. Ordering

-   Some systems (Kafka) guarantee order within partitions

------------------------------------------------------------------------

### Kafka Model

``` text
Producer → Topic → Consumer
```

-   Topics split into partitions
-   Consumers read independently
-   Supports replay of messages

------------------------------------------------------------------------

### Message Broker vs RPC

  Feature            Message Broker      RPC
  ------------------ ------------------- ---------------
  Communication      Async               Sync
  Coupling           Loose               Tight
  Failure handling   Built-in            Manual
  Use case           Events, pipelines   Service calls

------------------------------------------------------------------------

### Message Broker vs Actor Model

-   Actor model = logical abstraction
-   Broker = infrastructure
-   Distributed actor frameworks combine both

------------------------------------------------------------------------

## 3. Schema Evolution

### Backward Compatibility

-   New system can read old data

### Forward Compatibility

-   Old system can read new data

### Avro Evolution

-   Writer + reader schema
-   Supports aliases, defaults

### ProtoBuf Evolution

-   Field numbers critical
-   Add/remove fields safe

------------------------------------------------------------------------

## 4. Avro Key Concepts

### Union

``` json
["null", "long"]
```

-   Represents optional field
-   Encoded as: index + value

### Alias

-   Used for renaming fields
-   Works only for backward compatibility

------------------------------------------------------------------------

## 5. Avro vs ProtoBuf

  Feature             Avro          ProtoBuf
  ------------------- ------------- ----------
  Schema at runtime   Yes           No
  Encoding            Value-based   Tagged
  Evolution           Strong        Moderate
  Use case            Kafka/Data    RPC

------------------------------------------------------------------------

## 6. Kafka Serialization

-   Avro + Schema Registry (most common)
-   JSON (simple)
-   ProtoBuf (growing)

------------------------------------------------------------------------

## 7. RPC vs REST

### RPC

-   Function-based
-   High performance
-   Strong contracts

### REST

-   Resource-based
-   HTTP + JSON
-   Easy to debug

------------------------------------------------------------------------

## 8. Encryption

-   Both use TLS
-   Same security level

------------------------------------------------------------------------

## 9. Actor Model

-   Actors communicate via async messages
-   No shared memory
-   Distributed actors use network transparently

------------------------------------------------------------------------

## 10. Archival Storage

-   Data rewritten using latest schema
-   Stored in Avro / Parquet

------------------------------------------------------------------------

## 11. Legacy Systems

-   RMI, DCOM, CORBA
-   Problems: complexity, lock-in, poor evolution

------------------------------------------------------------------------

## Final Takeaways

-   Avro → Kafka, pipelines
-   ProtoBuf → RPC
-   BSON → MongoDB
-   Message brokers → async communication backbone
-   REST → public APIs
-   RPC → internal services
-   Actor model → scalable systems
