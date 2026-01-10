# Faults

When a component deviates from its expectations

# Failures

- When system stops working -> happens because of multiple faults
- System must be fault tolerant, faults can not be prevented 100%

# RAID

- Redundant array of independent disks
- To prevent hardware faults, redundant disk copies are created 
- copies within same machine, so not responsible for high availabilitys

# Software Faults

- One of service gets corrupted/slow it slows down overall system
- Cascading failures -> one service fault -> cause another to fail

### Idempotency Keys

- eg. a payment api called twice by mistake by user to deduct x amount, to prevent double payments-> an idempotency key is attached to request -> application checks if key already exists then returns previous response -> else perform the required operation

What operations need idempotency keys?
- Required

    - POST /create

    - Payments

    - Order creation

    - Resource provisioning

- Not required

    - GET

    - PUT (already idempotent by definition)

    - DELETE (usually idempotent)

# Avoiding Human Errors

- Faster rollback, gradual rollout of changes
- monitoring, telemetry

# Twitter Problem

- Twitter scale issue is **fan-out**, not raw tweet volume: ~12k writes/sec vs ~300k home timeline reads/sec.
- **Approach 1 (fan-out on read)**: store tweets globally; on read, fetch tweets of followed users and merge by time.
- Example SQL:
  ```sql
  SELECT tweets.*, users.* FROM tweets
  JOIN users ON tweets.sender_id = users.id
  JOIN follows ON follows.followee_id = users.id
  WHERE follows.follower_id = :current_user;
    ```

- Twitter’s scalability bottleneck is **fan-out** (many followers per user), not the raw number of tweets.
- **Approach 1: Fan-out on read** — tweets are stored once, and a user’s home timeline is built at read time by fetching and merging tweets from all followed users.
- This makes writes cheap but causes extremely heavy read load, which does not scale with hundreds of thousands of timeline requests per second.
- **Approach 2: Fan-out on write** — when a tweet is posted, it is pushed into each follower’s home timeline cache in advance.
- This makes timeline reads very fast, shifting most of the cost to write time.
- The downside is **write amplification**: a single tweet may generate millions of writes for users with huge follower counts.
- This follower skew makes pure fan-out on write impractical for celebrities.
- **Hybrid approach** — normal users use fan-out on write, while celebrity tweets are excluded from precomputation.
- Tweets from celebrities are fetched and merged at read time instead.
- This hybrid model balances read performance, write cost, and extreme fan-out scenarios.

- For hybrid basically merge the result of this sql query to home timeline

  ```sql
  SELECT tweets.*, users.* FROM tweets
  JOIN users ON tweets.sender_id = users.id
  WHERE users.is_celebrity = TRUE
  JOIN follows ON follows.followee_id = users.id
  WHERE follows.follower_id = :current_user;
    ```


# Latency vs Response Time

### Response time

Response time = latency + service time + network delays
its the end to end processing time

### Latency
Latency = queueing delay + other pre-service delays(CPU wait, I/O wait, free thread/connection wait)
in short request is waiting, but not doing any work

# Percentiles

- Service performance is measured in percentile duration of request, for eg. amazon uses p999 ie. 999 out of 1000 requests should be completed within SLA(service level agreement)
- Optimizing for much higer percentiles like p9999 is very costly and mostly out of control like network delays, CPU limitations etc.
- Head of line blocking - small number of slow requests block processing of further requests

## Tail Latency Amplification (Why p99 matters)

**Assume:**
- Each backend call has a **1% chance** of being slow (p99)
- A user request makes **N backend calls**
- probability = 1 - (0.99)^N

**Question:** What’s the chance *at least one* call is slow?

**Answer:**

- N = 1  → **1%**
- N = 10 → **~9.6%**
- N = 50 → **~39%**
- N = 100 → **~63%**

A “rare” slow call quickly becomes **common** for users.

**This effect is called _tail latency amplification_.**

TODO Note- read about correct algorithms for percentile computations


