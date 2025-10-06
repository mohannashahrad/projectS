# Ceph Internal Architecture Overview

             +------------------------------------+
             |          User App                  |
             +------------------------------------+
                               |
     +------------------------------------------------------+
     |            Ceph Client Interfaces / APIs             |
     |  - CephFS (POSIX File System)                        |
     |  - RBD (Block Device)                                |
     |  - RGW (Object Gateway - S3/Swift)                   |
     +------------------------------------------------------+
                               |
                         via librados
                               ↓
     +------------------------------------------------------+
     |                 RADOS (Core Layer)                   |
     |  - CRUSH algorithm: Data placement                   |
     |  - Object replication / erasure coding               |
     |  - Placement groups (PGs)                            |
     |  - Cluster membership, heartbeats                    |
     +------------------------------------------------------+
                               ↓
     +------------------------------------------------------+
     |          Ceph OSD Daemons (Object Storage Daemons)   |
     |  - Handle object I/O, replication, recovery           |
     |  - Participate in CRUSH placement                     |
     |  - Maintain local object metadata                     |
     +------------------------------------------------------+
                               ↓
     +------------------------------------------------------+
     |         Local Storage Engine (BlueStore/FileStore)   |
     |  - BlueStore: Directly manages raw devices            |
     |  - RocksDB: Metadata store                            |
     |  - Write-ahead logging (WAL), caching, GC             |
     +------------------------------------------------------+
                               ↓
                    Physical Devices (HDD/SSD/NVMe)


---

### Main Internal Components

| **Component**                              | **Description**                                           |
| ------------------------------------------ | --------------------------------------------------------- |
| **Placement Groups (PGs)**                 | Buckets mapping objects to OSD sets via CRUSH.            |
| **CRUSH algorithm**                        | Determines data placement based on cluster topology.      |
| **Replication / Erasure Coding Pipelines** | Writes must reach multiple OSDs or parity fragments.      |
| **Recovery & Backfill**                    | Re-replicates data after OSD failures or reweighting.     |
| **Scrubbing (Integrity Checks)**           | Background consistency verification across replicas.      |
| **Monitors & Managers (MON, MGR)**         | Maintain cluster metadata, configuration, and stats.      |

---

### Data Replication and Erasure Coding

- Each object is stored in a **Placement Group (PG)**, which maps to multiple OSDs.  
- Processes involved:
  - Coordination and quorum-based updates.
  - Periodic **scrubbing** to ensure consistency among replicas.

---

### Caching

- **BlueStore Cache:** In-memory buffer for data and metadata.  
- **RocksDB Block Cache:** Caches metadata blocks.  
- **Filesystem Cache:** (If using FileStore backend) OS-level page cache.  
- **Device-Level Cache:** SSD/NVMe controller DRAM or internal write cache.  

---

### Journaling / Write-Ahead Logging (WAL)

- Ensures **durability** and **atomicity** of writes.  
- **BlueStore:** Uses an internal WAL and RocksDB write-ahead log.  
- **FileStore:** Uses an external journal (often on a dedicated device).  

---

### Compaction and Garbage Collection

- **RocksDB Compaction:** Merges SSTables and removes obsolete keys.  
- **BlueStore Garbage Collection:** Reclaims freed extents after object deletions.  
- **SSD-Level GC:** Firmware-level block reclamation independent of Ceph.  

- Some notes about configuring GC in Ceph:  
- Configurations for object storage:
    - rgw_gc_max_objs (default 32) maximum number of objects that may be handled by garbage collection in one garbage collection processing cycle 
    - rgw_gc_obj_min_wait: minimum wait time before a deleted object may be removed and handled by GC (2 hours)
    - rgw_gc_processor_max_time: the maximum time between the beginning of two consecutive garbage collection processing cycles. (1 hour)
    - rgw_gc_processor_period: the cycle time for garbage collection processing. (1 hour)
    - rgw_gc_max_concurrent_io: The maximum number of concurrent IO operations that the RGW garbage collection thread will use when purging old data. (10)

---

### Scrubbing and Deep Scrubbing

- Detects silent data corruption and replica divergence.  
- **Scrub:** Compares object metadata (size, hash) across replicas.  
- **Deep Scrub:** Reads full object data to verify checksums.  

---

Would you like me to add another section summarizing **performance-impacting processes** (e.g., recovery, compaction, GC, scrubbing) and when they typically trigger during cluster operation? That would complement this nicely if you’re mapping internal processes to user-visible latency variations.
