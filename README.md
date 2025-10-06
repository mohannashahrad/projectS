# Project S*
## Storate System list
### F=FileStorage, B=BlockStorage, O=ObjectStorage,D=DataBase
HDFS (F)\
Ceph (FBO)\
postgres (D)\
MinIO (O)\
Apache Ozone (O)\
openstack swift (O)\
HBase (D)\
### Physical Storage
SSD\
HDD\
Flash Storage

## Transparent Performance-Disruptive Indirections
GC\
Caching (eviction rate , miss/hit ratios)\
Replication\
Redundancy (e.g.,EC) \ 
Write-ahead log (WAL) flushing \ 
Index rebuilds \ 
SSD wear-leveling \ 
Filesystem journaling

## Performance Goals
Avg/Median Latency\
Tail Latency\
Throughput\
Fairness\
(Timely) Predictability

## Reading Lists
SSD stuff?\
LinnOS: https://www.usenix.org/conference/osdi20/presentation/hao \
Synthesizing Adversarial Workload for NF: https://dl.acm.org/doi/pdf/10.1145/3230543.3230573 \
IODA: https://dl.acm.org/doi/pdf/10.1145/3477132.3483573 \
Pathological class for (A type of) GC: https://dl.acm.org/doi/pdf/10.1145/3720430 \
A GC-Aware Fair Scheduler for SSD: https://ieeexplore.ieee.org/stamp/stamp.jsp?tp=&arnumber=8863523 \
GC-Aware Request Steering for SSD-Based RAIDs: https://ieeexplore.ieee.org/stamp/stamp.jsp?arnumber=9000716



