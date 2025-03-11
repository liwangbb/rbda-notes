# Lecture 3 Hadoop Distributed File System
## Overview

The Hadoop Distributed File System is a **distributed file system** designed to run on **commodity hardware**.

It provides an interface **similar to a POSIX file system** (files and directories) but with **relaxed requirements**.

## Assumptions and goals
- Commodity hardware
  - **Hardware failure is the norm** rather than the exception. 
  - An HDFS instance may consist of thousands of servers. Each component has a non-trivial probability of failure. **Some component of HDFS is always non-functional**. 
  - Therefore, **detection of faults** and **quick, automatic recovery** from them is a core architectural goal of HDFS.
- Streaming data access
  - HDFS is designed more for **batch processing** than for **interactive access** by users. The emphasis is on **high throughput** rather than **low latency** of data access.
  - **Some POSIX semeantics are relaxed** to achieve high throughput.
- Large datasets
  - A typical file in HDFS is **gigabytes to terabytes** in size. Thus, HDFS is tuned to **suppport large files**.
  - It should provide **high aggregate data bandwidth**.
  - It should scale to **hundreds of nodes** in a single cluster.
  - It should support **tens of millions of files** in a single instance.
- Simple coherency model
  - HDFS applications need a **write-once, read-many** access model for files.
  - Files cannot be modified except for appending and truncating.
  - This assumption **simplifies data coherency issues** and enable high throughput data access. 
- Moving computation is cheaper than moving data
  - A computation requested by an application is **much more efficient if it is executed near the data it operates on**, especially for large datasets.
  - This **minimizes network congestion** and **increases the system throughput**.
  - It is often better to **mitigate the computation closer to where the data is located** rather than moving the data to where the application is running.
- Portability across heterogeneous hardware & software platforms
  - HDFS has been designeed to be **easily portable** across different platforms.
  - This facilitates **widespread adoption** of HDFS as a platform of choice for a large set of applications.