# Data Flow visualizations for RiakTS

<hr>

## Dynamic Ring Analysis

The following were visualizations I created to track data flow during
performance benchmarking runs.  In this case multiple YCSB harness
threads were used first to load time-ordered data into RiakTS, and
then to issue randomized queries against that data.  The ingest
activity is visualized on the left, and the query activity on the
right.

Each set of rings represents the entire RiakTS cluster -- in this case
there were three physical nodes (visualized as three concentric
rings), each node controlling a subset of 64 total hash partitions.
An additional outermost ring represents total activity against any
partition, regardless of which node controls it.

In the mp4 versions, partitions are lit as data (or queries) touch
those partitions, with intensity proportional to the total number of
records instantaneously ingested (or queried).  As the movies
progress, I display the cumulative totals for each partition in the
outermost ring, as a way of tracking the integrated distribution of
puts/queries over the course of the whole test (a uniform ring shows
that no hot-spotting has occurred, for example).

These were for two different workloads, one is cleary uniformly
distributed around the ring, while the other was definitely not. (That
was a bug in the test harness, which we used this tool to identify)

![YCSB Ordered Workload](https://github.com/erikleitch/dbvis/blob/master/images/YcsbQueryOrdered.png)

Full mp4 is here: https://github.com/erikleitch/dbvis/blob/master/mp4/YcsbQueryOrdered.mp4

![YCSB Hashed Workload](https://github.com/erikleitch/dbvis/blob/master/images/YcsbQueryHashed.png)

Full mp4 is here: https://github.com/erikleitch/dbvis/blob/master/mp4/YcsbQueryHashed.mp4

<hr>

## Query Sonograms

This last was a slightly different type of visualization I created to
track individual queries flowing through the system.  I call them
'query sonograms'.  As before, the concentric rings represent physical
nodes in the RiakTS cluster (most of the setups below were single-node
tests, while two were three-node clusters deployed on Softlayer), but
the movies are now tracking a single query for several hours worth of
data, distributed across the cluster with different time
quantizations.  The coordinating node lights up when it receives the
query request and doesn't go dark again until the query results have
been returned to the requestor.  During the query processing, the
relevant partitions are lit as subqueries hit the vnodes managing
those partitions.

Because each vnode managing a single partition runs as a separate
thread, the point of this video was to demonstrate the tradeoffs
between reducing your time quanta to take advantage of
parallel-processing resources available across your cluster.  The
optimal quantization of course will depend on the capabilities of your
machine, and on the topology of your cluster (single-node vs
multiple-node vs multiple-node run on distinct hosts).

![Query Sonograms](https://github.com/erikleitch/dbvis/blob/master/images/QuantumComp.png)

Full mp4 is here: https://github.com/erikleitch/dbvis/blob/master/mp4/QuantumComp.mp4
