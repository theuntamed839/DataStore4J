# DataStore4J
A thread-safe, high-performance key-value store, built on an LSM-tree architecture inspired by [LevelDB](https://github.com/google/leveldb), and written entirely in Java.

### Features:
* Implemented entirely in Java (no JNI or native code).
* Thread-safe with concurrency support.
* Zero-configuration, fully embeddable library.
* Supports memory-mapped files for efficient OS-level management and FileChannel-based I/O for portability
* Uses Arena to map and unmap memory-mapped files, avoiding potential memory leaks.
* Uses Write-Ahead Logging (WAL) before memtable flush

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

### Requirement:
* Java 22 or higher

### Usage:

```java
import io.github.theuntamed839.datastore4j.db.DB;
import io.github.theuntamed839.datastore4j.db.DataStore4J;
import io.github.theuntamed839.datastore4j.db.DbOptions;
...

Path dbPath = Files.createDirectory(Path.of("PathForDB"));
DbOptions opt = new DbOptions();
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

// get search stats
SearchStats searchStats = db.getSearchStats();

db.close();
```

### Docs:
* [Benchmarks](https://github.com/theuntamed839/DataStore4J/blob/main/BenchMark/readme.md)
* [Database Configuration Options](https://github.com/theuntamed839/DataStore4J/wiki/Database-Configuration-Options)
* [DB internals and Design](https://github.com/theuntamed839/DataStore4J/wiki)


### Limitations:
* Potential crash during compaction while adding files to the table, which may leave the database in an inconsistent state.
* Large keys (or occasional insertions of unusually large keys) can remain resident in memory if they happen to occupy critical pointer positions.
* Reader objects are managed without an LRU cache, which may lead to suboptimal memory utilization for large datasets.

### Planned Improvements
1. [ ] Replace the current Guava dependency with a custom internal Bloom filter implementation to minimize external dependencies.
2. [ ] Introduce smarter caching and eviction strategies for reader objects (e.g., LRU or adaptive policies).
3. [ ] Enhance crash recovery mechanisms to enable database restoration from any intermediate state.
4. [ ] Improve robustness for large datasets, including graceful handling of exceptions such as excessive open files or memory exhaustion.
5. [ ] Provide configurable selection of LZ4 implementations via the API.
6. [ ] [Implement a more efficient file search algorithm](https://github.com/theuntamed839/DataStore4J/blob/00c8de4c7551e1ae39052cb0fd3f5be8a9bd4c71/DataStore4J/src/main/java/io/github/theuntamed839/datastore4j/search/Search.java#L69)
7. [ ] Add support for custom key comparators.
8. [ ] Enhance concurrency, ensuring reads are fully independent of writes.