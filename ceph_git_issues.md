1. https://github.com/ceph/ceph/pull/61753
Newer algorithms to handle smaller read/write operations more efficiently
- Their profiling: Profiling showed the slowdown was due to increased CPU time and overhead in processing map and set data structures, which the new EC code relies on heavily.

Takeaway: EC layer became slower (≈10% IOPS drop) becasue of data structures using in the alg.

2. https://github.com/ceph/ceph/pull/63607
- The snaptrim process (which cleans up deleted snapshot objects) was highly inefficient when dealing with large numbers of objects. This cases This inefficiency caused: Sustained CPU utilization >60% on idle OSDs.
- Takeaway: snapshotting and deleting them could also be a target for performance issues

3. 
