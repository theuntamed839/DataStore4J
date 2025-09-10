### Benchmarks:
The following benchmarks were run on the system with the following specifications:
```text
OS: Ubuntu 20.04.6 LTS x86_64
CPU: Intel i7-10850H (12) @ 5.100GHz
RAM: 16 GB
Disk: PM9A1 NVMe Samsung 512GB
Swappiness: 60
Swap file size: 2 GB
```

**Note: The exact commands used to run each test are recorded at the top of the console output file for that benchmark**

### Write Benchmark:
JMH Benchmark results for writing 1 million and 5 million entries with fixed size (500 bytes key and 500 bytes value) and variable
size (up to 500 bytes key and up to 500 bytes value) data.

The benchmark mode is single shot (ss) meaning it measures the time taken for a single operation (in this case, writing all entries).
The results are expressed in seconds per operation (s/op).
```text
Benchmark                               (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score    Error  Units
BenchmarkWrite.writeFixedSizeData        DATASTORE4J       1000000        500          500    ss    9   14.046 ±  0.354   s/op
BenchmarkWrite.writeFixedSizeData        DATASTORE4J       5000000        500          500    ss    9   82.978 ±  2.685   s/op
BenchmarkWrite.writeFixedSizeData            ROCKSDB       1000000        500          500    ss    9    7.142 ±  0.177   s/op
BenchmarkWrite.writeFixedSizeData            ROCKSDB       5000000        500          500    ss    9   52.038 ±  4.161   s/op
BenchmarkWrite.writeFixedSizeData       LEVELDB_JAVA       1000000        500          500    ss    9   73.469 ±  3.995   s/op
BenchmarkWrite.writeFixedSizeData       LEVELDB_JAVA       5000000        500          500    ss    9  664.876 ± 20.200   s/op
BenchmarkWrite.writeFixedSizeData     LEVELDB_NATIVE       1000000        500          500    ss    9  109.612 ±  8.662   s/op
BenchmarkWrite.writeFixedSizeData     LEVELDB_NATIVE       5000000        500          500    ss    9  876.159 ± 17.701   s/op
BenchmarkWrite.writeVariableSizeData     DATASTORE4J       1000000        500          500    ss    9    9.739 ±  0.490   s/op
BenchmarkWrite.writeVariableSizeData     DATASTORE4J       5000000        500          500    ss    9   59.756 ±  1.478   s/op
BenchmarkWrite.writeVariableSizeData         ROCKSDB       1000000        500          500    ss    9    5.210 ±  0.196   s/op
BenchmarkWrite.writeVariableSizeData         ROCKSDB       5000000        500          500    ss    9   28.271 ±  0.623   s/op
BenchmarkWrite.writeVariableSizeData    LEVELDB_JAVA       1000000        500          500    ss    9   31.882 ±  2.180   s/op
BenchmarkWrite.writeVariableSizeData    LEVELDB_JAVA       5000000        500          500    ss    9  282.108 ±  7.598   s/op
BenchmarkWrite.writeVariableSizeData  LEVELDB_NATIVE       1000000        500          500    ss    9   38.070 ±  0.502   s/op
BenchmarkWrite.writeVariableSizeData  LEVELDB_NATIVE       5000000        500          500    ss    9  344.800 ±  2.446   s/op
```
In this benchmark, a lower `score` is better, as it indicating that less time was taken to complete the write operations.

If we rank the databases based on the time taken to write 5 million entries, the order is:

| Database | Avg time taken(in seconds) to write Fixed Data | Avg time taken(in seconds) to write Variable Data | 
|----|------------------------------------------------|---------------------------------------------------|
| ROCKSDB   | 52.038                                         | 28.271                                            |
| DATASTORE4J   | 82.978                                         | 59.756                                            |
| LEVELDB_JAVA   | 664.876                                        | 282.108                                           |
| LEVELDB_NATIVE   | 876.159                                        | 344.800                                           |

Based on these results, another test was run for 10 million entries with fixed-size data for RocksDB and DataStore4J:

```text
Benchmark                          (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkWrite.writeFixedSizeData   DATASTORE4J      10000000        500          500    ss       164.284           s/op
BenchmarkWrite.writeFixedSizeData       ROCKSDB      10000000        500          500    ss       167.337           s/op
```
DataStore4J demonstrates write performance on par with RocksDB for 10 million fixed-size entries.

### Read Benchmark:

#### Fixed Size Data Read:
JMH Benchmark results for reading 1 million and 5 million entries with fixed size (500 bytes key and 500 bytes value) data.

The mode is Single Shot.
```text
Benchmark                      (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score    Error  Units
BenchmarkRead.linearSearch      DATASTORE4J       1000000        500          500    ss    9    8.012 ±  0.405   s/op
BenchmarkRead.linearSearch      DATASTORE4J       5000000        500          500    ss    9   75.853 ±  3.917   s/op
BenchmarkRead.linearSearch          ROCKSDB       1000000        500          500    ss    9   25.072 ±  0.186   s/op
BenchmarkRead.linearSearch          ROCKSDB       5000000        500          500    ss    9   49.355 ±  1.989   s/op
BenchmarkRead.linearSearch     LEVELDB_JAVA       1000000        500          500    ss    9   21.859 ±  0.353   s/op
BenchmarkRead.linearSearch     LEVELDB_JAVA       5000000        500          500    ss    9  299.488 ± 14.869   s/op
BenchmarkRead.linearSearch   LEVELDB_NATIVE       1000000        500          500    ss    9    2.776 ±  0.102   s/op
BenchmarkRead.linearSearch   LEVELDB_NATIVE       5000000        500          500    ss    9   80.667 ±  0.688   s/op
BenchmarkRead.randomSearch      DATASTORE4J       1000000        500          500    ss    9    8.623 ±  0.226   s/op
BenchmarkRead.randomSearch      DATASTORE4J       5000000        500          500    ss    9   80.197 ±  4.500   s/op
BenchmarkRead.randomSearch          ROCKSDB       1000000        500          500    ss    9   25.242 ±  0.272   s/op
BenchmarkRead.randomSearch          ROCKSDB       5000000        500          500    ss    9   50.369 ±  0.841   s/op
BenchmarkRead.randomSearch     LEVELDB_JAVA       1000000        500          500    ss    9   23.555 ±  4.565   s/op
BenchmarkRead.randomSearch     LEVELDB_JAVA       5000000        500          500    ss    9  348.684 ± 28.951   s/op
BenchmarkRead.randomSearch   LEVELDB_NATIVE       1000000        500          500    ss    9    2.847 ±  0.045   s/op
BenchmarkRead.randomSearch   LEVELDB_NATIVE       5000000        500          500    ss    9   81.951 ±  2.001   s/op
BenchmarkRead.reverseSearch     DATASTORE4J       1000000        500          500    ss    9    8.499 ±  0.152   s/op
BenchmarkRead.reverseSearch     DATASTORE4J       5000000        500          500    ss    9   76.717 ±  3.234   s/op
BenchmarkRead.reverseSearch         ROCKSDB       1000000        500          500    ss    9   25.097 ±  0.303   s/op
BenchmarkRead.reverseSearch         ROCKSDB       5000000        500          500    ss    9   50.635 ±  2.140   s/op
BenchmarkRead.reverseSearch    LEVELDB_JAVA       1000000        500          500    ss    9   25.499 ±  7.801   s/op
BenchmarkRead.reverseSearch    LEVELDB_JAVA       5000000        500          500    ss    9  309.199 ± 19.794   s/op
BenchmarkRead.reverseSearch  LEVELDB_NATIVE       1000000        500          500    ss    9    2.662 ±  0.066   s/op
BenchmarkRead.reverseSearch  LEVELDB_NATIVE       5000000        500          500    ss    9   81.255 ±  0.920   s/op
```
Lower `score` is better, indicating that less time was taken to complete the read operations.

Ranking the databases for 5 million entries:

| Database | Avg time taken(in seconds) Sequential read | Avg time taken(in seconds) Random read | Avg time taken(in seconds) Reverse read |
|----|--------------------------------------------|----------------------------------------|-----------------------------------------|
| ROCKSDB   | 49.355                                    | 50.369                               | 50.635 |
| DATASTORE4J   | 75.853                                     | 80.197                                | 76.717 |
| LEVELDB_NATIVE   | 80.667                                    | 81.951                               | 81.255 |
| LEVELDB_JAVA   | 299.488                                   | 348.684                               |309.199 |

Another test was run for 10 million entries with RocksDB, DataStore4J, and LevelDB Native:

```text
Benchmark                     (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkRead.randomSearch     DATASTORE4J      10000000        500          500    ss       740.025           s/op
BenchmarkRead.randomSearch         ROCKSDB      10000000        500          500    ss       525.133           s/op
BenchmarkRead.randomSearch  LEVELDB_NATIVE      10000000        500          500    ss       804.094           s/op
```

RocksDB continues to lead in read performance, with DataStore4J close behind.

#### Variable Size Data Read:
JMH Benchmark results for reading 1 million and 5 million entries with variable size (up to 500 bytes key and up to 500 bytes value)
data.

Mode is Single Shot.
```text
Benchmark                                      (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score    Error  Units
BenchmarkVariableSizeDataRead.linearSearch      DATASTORE4J       1000000        500          500    ss    9    5.692 ±  0.111   s/op
BenchmarkVariableSizeDataRead.linearSearch      DATASTORE4J       5000000        500          500    ss    9   58.093 ±  2.579   s/op
BenchmarkVariableSizeDataRead.linearSearch          ROCKSDB       1000000        500          500    ss    9    8.872 ±  0.067   s/op
BenchmarkVariableSizeDataRead.linearSearch          ROCKSDB       5000000        500          500    ss    9  118.848 ± 62.785   s/op
BenchmarkVariableSizeDataRead.linearSearch     LEVELDB_JAVA       1000000        500          500    ss    9   14.454 ±  0.400   s/op
BenchmarkVariableSizeDataRead.linearSearch     LEVELDB_JAVA       5000000        500          500    ss    9  156.036 ± 12.253   s/op
BenchmarkVariableSizeDataRead.linearSearch   LEVELDB_NATIVE       1000000        500          500    ss    9    2.887 ±  0.042   s/op
BenchmarkVariableSizeDataRead.linearSearch   LEVELDB_NATIVE       5000000        500          500    ss    9   52.426 ±  0.539   s/op
BenchmarkVariableSizeDataRead.randomSearch      DATASTORE4J       1000000        500          500    ss    9    6.091 ±  0.235   s/op
BenchmarkVariableSizeDataRead.randomSearch      DATASTORE4J       5000000        500          500    ss    9   62.111 ±  1.265   s/op
BenchmarkVariableSizeDataRead.randomSearch          ROCKSDB       1000000        500          500    ss    9    8.911 ±  0.195   s/op
BenchmarkVariableSizeDataRead.randomSearch          ROCKSDB       5000000        500          500    ss    9  133.302 ± 26.020   s/op
BenchmarkVariableSizeDataRead.randomSearch     LEVELDB_JAVA       1000000        500          500    ss    9   14.651 ±  0.496   s/op
BenchmarkVariableSizeDataRead.randomSearch     LEVELDB_JAVA       5000000        500          500    ss    9  179.684 ± 28.393   s/op
BenchmarkVariableSizeDataRead.randomSearch   LEVELDB_NATIVE       1000000        500          500    ss    9    2.962 ±  0.043   s/op
BenchmarkVariableSizeDataRead.randomSearch   LEVELDB_NATIVE       5000000        500          500    ss    9   54.139 ±  0.683   s/op
BenchmarkVariableSizeDataRead.reverseSearch     DATASTORE4J       1000000        500          500    ss    9    5.772 ±  0.113   s/op
BenchmarkVariableSizeDataRead.reverseSearch     DATASTORE4J       5000000        500          500    ss    9   58.680 ±  2.750   s/op
BenchmarkVariableSizeDataRead.reverseSearch         ROCKSDB       1000000        500          500    ss    9    8.781 ±  0.305   s/op
BenchmarkVariableSizeDataRead.reverseSearch         ROCKSDB       5000000        500          500    ss    9  133.540 ± 25.095   s/op
BenchmarkVariableSizeDataRead.reverseSearch    LEVELDB_JAVA       1000000        500          500    ss    9   14.446 ±  0.637   s/op
BenchmarkVariableSizeDataRead.reverseSearch    LEVELDB_JAVA       5000000        500          500    ss    9  162.485 ± 28.680   s/op
BenchmarkVariableSizeDataRead.reverseSearch  LEVELDB_NATIVE       1000000        500          500    ss    9    2.832 ±  0.031   s/op
BenchmarkVariableSizeDataRead.reverseSearch  LEVELDB_NATIVE       5000000        500          500    ss    9   52.390 ±  0.998   s/op
```
Surprisingly, RocksDB seems to be slower with variable size data for reads, while LevelDB Native shows a slight edge over DataStore4J.

| Database | Avg time taken(in seconds) Sequential read | Avg time taken(in seconds) Random read | Avg time taken(in seconds) Reverse read |
|----|--------------------------------------------|----------------------------------------|-----------------------------------------|
| LEVELDB_NATIVE  | 52.426                                     | 54.139                               | 52.390 |
| DATASTORE4J  | 58.093                                     | 62.111                                | 58.680 |
| ROCKSDB  | 118.848                                           | 133.302                               |133.540 |
| LEVELDB_JAVA  | 156.036                                     | 179.684                               | 162.485 |

Another test with 10 million entries confirmed RocksDB’s lead, with DataStore4J and LevelDB Native following closely.

```text
Benchmark                                     (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkVariableSizeDataRead.randomSearch     DATASTORE4J      10000000        500          500    ss       175.823           s/op
BenchmarkVariableSizeDataRead.randomSearch         ROCKSDB      10000000        500          500    ss       122.458           s/op
BenchmarkVariableSizeDataRead.randomSearch  LEVELDB_NATIVE      10000000        500          500    ss       170.211           s/op
```
#### Iteration Behavior:
Observing the JMH iterations makes it clear that RocksDB and LevelDB Native perform some form of caching in each iteration.
I am not an expert on these databases, but it seems they also take advantage of the OS page cache with kernel-level hints on read
patterns, which benefits them significantly ?

```text
Iteration from BenchmarkRead.txt
# Benchmark: io.github.theuntamed839.BenchmarkRead.randomSearch
# Parameters: (dbProvider = ROCKSDB, entryCount = 5000000, keySize = 500, valueSize = 500)

# Fork: 1 of 3
# Warmup Iteration   1: 68.712 s/op
# Warmup Iteration   2: 51.240 s/op
# Warmup Iteration   3: 51.046 s/op
Iteration   1: 50.854 s/op
Iteration   2: 49.290 s/op
Iteration   3: RocksDBAdaptor_a4f0bf56-0cf7-4bbe-891d-3b00f8cdafdf Folder size: 4855
49.890 s/op

# Benchmark: io.github.theuntamed839.BenchmarkRead.randomSearch
# Parameters: (dbProvider = LEVELDB_NATIVE, entryCount = 5000000, keySize = 500, valueSize = 500)

# Fork: 1 of 3
# Warmup Iteration   1: 105.123 s/op
# Warmup Iteration   2: 81.080 s/op
# Warmup Iteration   3: 80.965 s/op
Iteration   1: 80.857 s/op
Iteration   2: 80.923 s/op
Iteration   3: NativeLevelDBAdaptor_7912de59-9c72-4656-b0a9-f5a078d939d2 Folder size: 5020
80.617 s/op

# Benchmark: io.github.theuntamed839.BenchmarkRead.randomSearch
# Parameters: (dbProvider = DATASTORE4J, entryCount = 5000000, keySize = 500, valueSize = 500)

# Fork: 1 of 3
# Warmup Iteration   1: 80.361 s/op
# Warmup Iteration   2: 82.425 s/op
# Warmup Iteration   3: 80.628 s/op
Iteration   1: 80.235 s/op
Iteration   2: 82.443 s/op
Iteration   3: DataStore4JAdaptor_aec10971-6f24-4360-91d1-89c06803ade9 Folder size: 5196
80.949 s/op
```

With each warmup iteration and measurement iteration for RocksDB and LevelDB Native,
the time taken decreases significantly, indicating effective data caching.
DataStore4J, on the other hand, remained in the same speed bracket all iterations, something to take note of.

We had a benchmark specifically to test this scenario.
#### Fixed Size Data Read (Fresh DB for each iteration):
JMH Benchmark results for reading 5 million entries with Fixed size (500 bytes key and 500 bytes value) data. Mode is Single Shot.
```text
Benchmark                          (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkFreshRead.randomSearch     DATASTORE4J       5000000        500          500    ss        72.444           s/op
BenchmarkFreshRead.randomSearch         ROCKSDB       5000000        500          500    ss        75.743           s/op
BenchmarkFreshRead.randomSearch    LEVELDB_JAVA       5000000        500          500    ss       353.250           s/op
BenchmarkFreshRead.randomSearch  LEVELDB_NATIVE       5000000        500          500    ss       109.559           s/op
```
| Database | Avg time taken(in seconds) random read |
|----|---------------------------------------|
| DATASTORE4J  | 72.444                                |  
| ROCKSDB  | 75.743                                | 
| LEVELDB_NATIVE | 109.559                        |
| LEVELDB_JAVA  |          353.250                             | 

DataStore4J leads in this benchmark, where each iteration starts with a fresh database.

But again this is not a very realistic scenario as in real world applications the database will be used over a period of time
and not created afresh each time.

### Concurrency Benchmark:
One of the main features of DataStore4J is its concurrency support. It allows multiple threads to read and write to the database
simultaneously without any external locking mechanism.

#### Concurrent Writes:
JMH Benchmark results for concurrent writes with 12 threads writing to a pre-populated database with 5 million entries with
fixed size (500 bytes key and 500 bytes value) data.

The benchmark mode is throughput (thrpt) meaning it measures the number of
operations per second (ops/s).
```text
Benchmark                                               (dbProvider)  (entryCount)  (keySize)  (valueSize)   Mode  Cnt      Score       Error  Units
BenchmarkConcurrentWrite.prepopulatedConcurrentWrite     DATASTORE4J       5000000        500          500  thrpt   30  52899.349 ±  3310.037  ops/s
BenchmarkConcurrentWrite.prepopulatedConcurrentWrite         ROCKSDB       5000000        500          500  thrpt   30  43255.954 ± 29482.900  ops/s
BenchmarkConcurrentWrite.prepopulatedConcurrentWrite    LEVELDB_JAVA       5000000        500          500  thrpt   30   6394.339 ±  1569.415  ops/s
BenchmarkConcurrentWrite.prepopulatedConcurrentWrite  LEVELDB_NATIVE       5000000        500          500  thrpt   30   5111.540 ±   920.209  ops/s
```
In this benchmark, higher `score` is better, indicating that more operations were completed per second.

| Database | Avg operations per second (ops/s) |
|----|-----------------------------------|
| DATASTORE4J  | 52899.349                        |
| ROCKSDB  | 43255.954                        |
| LEVELDB_JAVA  | 6394.339                         |
| LEVELDB_NATIVE  | 5111.540                         |

DataStore4J delivered stable, predictable throughput with narrow error bounds and a healthy p99 close to its mean.

While the average score for RocksDB is competitive, the most striking aspect of its performance is the exceptionally high error
margin of ±29,482.90 ops/s. This indicates that the results were highly unreliable and inconsistent from one run to another.
A closer look at the percentile data from the raw JMH results provides clear evidence of this inconsistency:

| Percentile | DataStore4J (ops/s) | RocksDB (ops/s) |
|----|----------------------------|-----------|
| 50th (Median)  | 52932                      | 14822|
| 99th  | 62526                | 119303 |

The table shows a massive gap between RocksDB's median performance and its 99th percentile.
This means that a significant portion of its benchmark runs were extremely slow, even though the peak performance was high.

#### Concurrent Reads:
JMH benchmark results for concurrent reads using 12 threads on a pre-populated database containing 5 million entries with fixed-size
data (500-byte key and 500-byte value). The benchmark performs random reads from the existing list of inserted keys.

Benchmark mode is throughput (thrpt).
```text
Benchmark                                   (dbProvider)  (entryCount)  (keySize)  (valueSize)   Mode  Cnt       Score        Error  Units
BenchmarkConcurrentRead.concurrentSearch     DATASTORE4J       5000000        500          500  thrpt   30  294969.277 ±  11358.105  ops/s
BenchmarkConcurrentRead.concurrentSearch         ROCKSDB       5000000        500          500  thrpt   30  422916.755 ± 110635.047  ops/s
BenchmarkConcurrentRead.concurrentSearch    LEVELDB_JAVA       5000000        500          500  thrpt   30   32234.258 ±   4742.068  ops/s
BenchmarkConcurrentRead.concurrentSearch  LEVELDB_NATIVE       5000000        500          500  thrpt   30   73201.828 ±   1440.878  ops/s
```
Higher `score` is better.

| Database | Avg operations per second (ops/s) |
|----|-----------------------------------|
| ROCKSDB  | 422916.755                        |
| DATASTORE4J  | 294969.277                       |
| LEVELDB_NATIVE  | 73201.828                        |
| LEVELDB_JAVA  | 32234.258                       |

Comparing the percentile data for the top two performers reinforces this point:

| Percentile | DataStore4J (ops/s) | RocksDB (ops/s) |
|----|----------------------------|-----------|
| 50th (Median)  | 303476                      | 478622|
| 99th  | 315260                | 583600 |
Although RocksDB's median and 99th percentile are higher than DataStore4J's, the significant spread between its own median and
99th percentile indicates that a large number of its reads were much slower than its peak performance.

### Database Folder Size Comparison
This table compares the on-disk size of the database folders after populating each database with 10 million key-value pairs, where both the key and value are 500 bytes.

| Database | Folder Size(MB) |
|-------|-----------------|
| RocksDB | 9712            |
| LEVELDB_NATIVE | 10156           |
| DATASTORE4J     | 10393           |

The results show that RocksDB is the most space-efficient of the three databases, with a folder size of 9,712 MB.
All three databases are relatively close in size, which is expected given that the raw data (10M entries * 1KB/entry)
is approximately 10GB.

Another point to note is that Disk seeks and reading from disk is much slower than reading from memory. Some thing which DataStore4J needs to work on.

Summary:

Writes: DataStore4J competes closely with RocksDB for large-scale writes.

Reads: RocksDB leads overall, though DataStore4J remains competitive and more consistent under some conditions.

Concurrency: DataStore4J excels in stable, concurrent write throughput, while RocksDB shows higher variability.

Space Efficiency: RocksDB is slightly more compact, though differences are minor.

Improvement Areas: DataStore4J can enhance read performance and caching strategies to better match RocksDB. and improve in efficiently compressing data on disk.
