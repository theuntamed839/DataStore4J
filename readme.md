# DataStore4J
**DataStore4J** A thread-safe, high-performance key-value store, built on an LSM-tree architecture inspired by [LevelDB](https://github.com/google/leveldb), and written entirely in Java.

### Features:
* Implemented entirely in Java (no JNI or native code).
* Thread-safe with robust concurrency support.
* Zero-configuration, fully embeddable library.
* Supports memory-mapped files for efficient OS-level management and FileChannel-based I/O for portability
* Compatible with custom Java file systems (e.g., [JIMFS](https://github.com/google/jimfs)) for in-memory execution, useful in testing and simulations.
* Uses Write-Ahead Logging (WAL) before memtable flush
* Uses in-memory indexes and Bloom filters to do quick searches.

### Getting started:

It is available in Maven Central as [io.github.theuntamed839:DataStore4J](https://central.sonatype.com/artifact/io.github.theuntamed839/DataStore4J).

### Maven:

```
<dependency>
    <groupId>io.github.theuntamed839</groupId>
    <artifactId>DataStore4J</artifactId>
    <version>0.1.0</version>
</dependency>
```
#### Gradle:

    implementation("io.github.theuntamed839:DataStore4J:0.1.0")

### Usage:

```java
// TODO test the below code
import io.github.theuntamed839.datastore4j.db.DB;
import io.github.theuntamed839.datastore4j.db.DataStore4J;
import io.github.theuntamed839.datastore4j.db.DbOptions;
...

Path dbPath = Files.createDirectory(Path.of("PathForDB"));
DbOptions opt = new DbOptions();
// opt.disallowUseOfMMap(); // to disable mmap
DB db = new DataStore4J(dbPath, opt);

byte[] key = "key".getBytes();
byte[] value = "value".getBytes();

// write
db.put(key, value);

// read
byte[] result = db.get(key);

// update
db.put(key, "newValue".getBytes());

// delete
db.delete(key);

db.close();
```
### Benchmarks:
Specs -
```text
OS: Ubuntu 20.04.6 LTS x86_64
CPU: Intel i7-10850H (12) @ 5.100GHz
RAM: 16 GB
Disk: PM9A1 NVMe Samsung 512GB
Swappiness: 60
Swap file size: 2 GB
```

**Note: All the commands used for running each test is added to the console output file of each benchmark at the top.**

### Write Benchmark:
JMH Benchmark results for writing 1 million and 5 million entries with fixed size (500 bytes key and 500 bytes value) and variable size (up to 500 bytes key and up to 500 bytes value) data.
The benchmark mode is single shot (ss) meaning it measures the time taken for a single operation (in this case, writing all entries) which translates to seconds per operation (s/op).
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
In this particular benchmark, lower `score` is better, indicating that less time was taken to complete the write operations.

If we have to rank the databases based on the time taken to write 5 million entries, the ranking would be as follows:

| Database | Avg time taken(in seconds) to write Fixed Data | Avg time taken(in seconds) to write Variable Data | 
|----|------------------------------------------------|---------------------------------------------------|
| ROCKSDB   | 52.038                                         | 28.271                                            |
| DATASTORE4J   | 82.978                                         | 59.756                                            |
| LEVELDB_JAVA   | 664.876                                        | 282.108                                           |
| LEVELDB_NATIVE   | 876.159                                        | 344.800                                           |

Based on the top results I ran another test for **10 million** entries for ROCKSDB and DATASTORE4J with fixed size data.

```text
Benchmark                          (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkWrite.writeFixedSizeData   DATASTORE4J      10000000        500          500    ss       164.284           s/op
BenchmarkWrite.writeFixedSizeData       ROCKSDB      10000000        500          500    ss       167.337           s/op
```
DataStore4J demonstrates write performance on par with RocksDB for 10 million fixed-size entries.

### Read Benchmark:

#### Fixed Size Data Read:
JMH Benchmark results for reading 1 million and 5 million entries with fixed size (500 bytes key and 500 bytes value) data. Again the mode is Single Shot.
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

If we have to rank the databases based on the time taken to write 5 million entries, the ranking would be as follows:

| Database | Avg time taken(in seconds) Sequential read | Avg time taken(in seconds) Random read | Avg time taken(in seconds) Reverse read |
|----|--------------------------------------------|----------------------------------------|-----------------------------------------|
| ROCKSDB   | 49.355                                    | 50.369                               | 50.635 |
| DATASTORE4J   | 75.853                                     | 80.197                                | 76.717 |
| LEVELDB_JAVA   | 299.488                                   | 348.684                               |309.199 |
| LEVELDB_NATIVE   | 80.667                                    | 81.951                               | 81.255 |

Based on the top results I ran another test for **10 million** entries for ROCKSDB, DATASTORE4J and LevelDB Native with fixed size data.

```text
Benchmark                     (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkRead.randomSearch     DATASTORE4J      10000000        500          500    ss       740.025           s/op
BenchmarkRead.randomSearch         ROCKSDB      10000000        500          500    ss       525.133           s/op
BenchmarkRead.randomSearch  LEVELDB_NATIVE      10000000        500          500    ss       804.094           s/op
```

RocksDB continues to lead in read performance for 10 million fixed-size entries, with DataStore4J following closely behind.

#### Variable Size Data Read:
JMH Benchmark results for reading 1 million and 5 million entries with variable size (up to 500 bytes key and up to 500 bytes value) data. Mode is Single Shot.
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
Quite surprising, RocksDB seems to be slower with variable size data for reads and Native LevelDB seems to be having some edge over DataStore4J.

| Database | Avg time taken(in seconds) Sequential read | Avg time taken(in seconds) Random read | Avg time taken(in seconds) Reverse read |
|----|--------------------------------------------|----------------------------------------|-----------------------------------------|
| LEVELDB_NATIVE  | 52.426                                     | 54.139                               | 52.390 |
| DATASTORE4J  | 58.093                                     | 62.111                                | 58.680 |
| ROCKSDB  | 118.848                                           | 133.302                               |133.540 |
| LEVELDB_JAVA  | 156.036                                     | 179.684                               | 162.485 |

Based on the top results, I ran another test with **10 million** entries using RocksDB, DataStore4J, and LevelDB Native. The test used fixed-size data and a random search workload to better simulate real-world usage.

```text
Benchmark                                     (dbProvider)  (entryCount)  (keySize)  (valueSize)  Mode  Cnt    Score   Error  Units
BenchmarkVariableSizeDataRead.randomSearch     DATASTORE4J      10000000        500          500    ss       175.823           s/op
BenchmarkVariableSizeDataRead.randomSearch         ROCKSDB      10000000        500          500    ss       122.458           s/op
BenchmarkVariableSizeDataRead.randomSearch  LEVELDB_NATIVE      10000000        500          500    ss       170.211           s/op
```
RocksDB continues to lead in read performance for 10 million variable-size entries, with LEVELDB_NATIVE and DataStore4J following behind, both showing competitive performance.

Observing the JMH iterations makes it clear that RocksDB and LevelDB Native perform some form of caching in each iteration. I am not an expert on these databases, but it seems they also take advantage of the OS page cache with kernel-level hints on read patterns, which benefits them significantly ?

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
We had a benchmark test specifically to test this scenario.
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
|----|-------------|
| DATASTORE4J  | 72.444      |  
| ROCKSDB  | 75.743      | 
|  LEVELDB_NATIVE | 353.250     |
| LEVELDB_JAVA  | 109.559     | 

DataStore4J outperforms LevelDB Native in read performance when each iteration starts with a fresh database.

But again this is not a very realistic scenario as in real world applications the database will be used over a period of time and not created afresh each time.

### Concurrency Benchmark:
One of the main features of DataStore4J is its concurrency support. It allows multiple threads to read and write to the database simultaneously without any external locking mechanism.

#### Concurrent Writes
JMH Benchmark results for concurrent writes with 12 threads writing to a pre-populated database with 5 million entries with fixed size (500 bytes key and 500 bytes value) data. The benchmark mode is throughput (thrpt) meaning it measures the number of operations per second (ops/s).
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

While the average score for RocksDB is competitive, the most striking aspect of its performance is the exceptionally high error margin of ±29,482.90 ops/s. This indicates that the results were highly unreliable and inconsistent from one run to another.
A closer look at the percentile data from the raw JMH results provides clear evidence of this inconsistency:

| Percentile | DataStore4J (ops/s) | RocksDB (ops/s) |
|----|----------------------------|-----------|
| 50th (Median)  | 52932                      | 14822|
| 99th  | 62526                | 119303 |

The table shows a massive gap between RocksDB's median performance and its 99th percentile. This means that a significant portion of its benchmark runs were extremely slow, even though the peak performance was high.

#### Disk size consumed for each test. WRITE

### Documentation:
  * [DB internals and Design](https://github.com/theuntamed839/DataStore4J/wiki) 
  * [More info on benchmarks](// link to read me)


### Limitations:
* Potential crash during compaction while adding files to the table, which may leave the database in an inconsistent state.
* Large keys (or sporadic insertions of unusually large keys) can remain resident in memory if they happen to occupy critical pointer positions.
* Reader objects are managed without an LRU cache, which may lead to suboptimal memory utilization for large datasets.

### Planned Improvements
1. [ ] Replace the current Guava dependency with an internal Bloom filter implementation to reduce external dependencies.
2. [ ] Introduce smarter caching and eviction strategies for reader objects (e.g., LRU or adaptive policies).
3. [ ] Enhance crash recovery mechanisms to enable database restoration from any intermediate state.
4. [ ] Improve robustness for large datasets, including graceful handling of exceptions such as excessive open files or memory exhaustion.
5. [ ] Provide configurable selection of LZ4 implementations via the API.
6. [ ] Implement a more efficient file search algorithm.