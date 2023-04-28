# Databases 

Ref:
`Jamie Brandon What is a database TigerBeetle talk`
`Database Engineering by Meta`
`Expert Mysql`
`Database System Concepts 7th Edition`
`CMU: Intro to Databases Systems`



## Introduction

- Organized collection of inter-related data that models some asoect of real-world.
- Long lived data.
- Parts:
  - Storage Engine.
    - intel memory lantency checker.
    - prefetching
    - pipelining
    - speculation
    - fio 
      - disk perf tool
    - ordering
    - fsync
    - reliability
    - send code to data vs data to code??? cover for latency
    - why are query languages weird?
  - Concurrency control.
  - Query Language
- Concerns:
  - Data Integrity.
  - Implementation
  - Durability
- DBMS - software that allows applications to store and analyze information in a database.
       - supports definition, creation, querying, update and administration in accordance to a data model.

## Data models

- Data models is a collection of concepts for describing the data in a database.
- A schema is a description of a particular collection of data using a given data model.
- Types:
    - Relational
      - Defines a database abstraction based on relations to avoid maintenance overhead.
      - A relation is an unordered set that contain the r/ship of attributes that represent entities.
      - A tuple is a set of attribute values(domain) in the relation.
      - A relations primary key uniquely indentifies a single tuple.
      - A foreign key specifies that an attribute from one relation has to map to a tuple in another relation.
      - Relational Algebra 
        - defines the high level steps of how to compute a query
        - fundamental operations to retrieve and manipulate tuples in a relation.
    - Key/Value
    - Graph
    - Document/ Object
    - Wide Column
    - Array/ Matrix/ Vector

## SQL

- Current standard is SQL:2016
- DML - Data Manipulation Language
- DDL - Data Definition Language
- DCL - Data Control Language
- SQL is based on bags(duplicates) not sets(no duplicates)
- Aggregates(AVG, MIN, MAX, SUM, COUNT, DISTINCT)
- GROUP BY and HAVING - filter after aggregation
- LIKE - string matching
- string operations - CONCAT
- DATE/ TIME OPS
- Unix Epoch 01-01-1970
- Output redirection
- LIMIT, OFFSET
- Nested Queries - difficult to optimize.
- EXISTS
- Window Functions
- Common Table Expressions - auxiliary statements for use in larger query.

## Anatomy of a DB.

- Good to understand how best to optimize a server and even how to utilize its features.
- Essential when modifying or extending its features

### Types of DBs

- Object Oriented Database systems.
  - access via a programmatic interface.
- Object Relational 
- Relational

## Structure

- Client Apps
  - Database connectors.
    - based on Open Database Connectivity model.
    - include client access, API, db driver
- Query Interface
  - i.e SQL
- Query Processing
  - query shipping
  - logical vs physical models of database design
  - query tree - turned from a logical plan to physical plan.
  - Steps
    - Parsing
    - Query Validation
    - Optimization
    - Plan Generation/Compilation
    - Execution
  - How does the query optimizer work??
    - Cost based optimization
    - Heuristic optimization
    - Semantic optimization
    - Parametric optimization
- Internal Representation of queries.
- Query Execution
  - Iterative
    - generate iterative programs from algebra-based query specs.
    - defined compiled functional primitives that are then combined in a call stack.
  - Interpretative
    - form query exec using exisiting compiled abstractions of basic operations.
    - reconstructed as a queue of method calls, which are each taken off queue and processed.
*compiled here means one thats been optimised and stored for future execution not actually compiled*
- File access
- A storage engine is designed to read and write data mechanism that provides some unique benefits to the user.
  - Goal is to find data we want quickly and efficiently without scanning more blocks than necessary.
  - Storage strategies
  - Buffering mechanisms
  - Index mechanisms
- Query results.


## MySQL structure

- SQL Interface
  - multi-threaded
- Parser
  - implemented in YACC and Lex
- Query Optimizer
- Query Execution
  - handled by a set of library methods designed to implement a particular query.
- Query Cache
  - caches query structure and query results
  - on by default
- Cache and Buffers
  - Table cache, Unireg(data chunking mechanism)
  - .frm files, contain metadata for the table.
  - Buffer pool used by Innodb storage engine to cache table and index data.
  - Record cache, enhance sequential reads form storage engines, works like
  - LRU algorithm
  - Privilege cache, store grant data on user account, FILO cache
  - Hostname cache
- File Access and Pluggable storage engines.
  - plugin - sth that can be changed at runtime.
  - MyISAM - fast access, read heavy, concurrency, advanced caching and indexing 
  - InnoDb - High reliability and ACID transactional support
  - Memory - valid during session.
  - Merge - couple of MyIsam with the same structure
  - Archive - large amounts in compressed format.
  - Federated - merge across databases.
  - Cluster/NDB - high availability and highperfromance environment. 
  - CSV
  - Blackhole - store data you dont want to save, mostly used to test.
  - Custom - any you can create to enhance your db server.


CMU PATH - Storage -> Execution -> Concurrency control -> Recovery -> Distributed Dbs

**Query Planning -> Operator Execution -> Access Methods -> Buffer Pool Manager -> Disk Manager**.

## Storage

### Disk based Architecture 

- DBMS assumes the primary storage is non-volatile disk.
- Manage movement of data between volatile to non-volatile.
- *intel optane - persistence memory.
- Access times:
  - LI Cache - 1 ns.
  - L2 Cache - 4 ns.
  - DRAM - 100 ns.
  - SSD - 16,000 ns.
  - HDD - 2,000,000 ns.
  - Network Storage - 50,000,000 ns.
  - Tape Archives - 1B ns.
- Sequential vs Random Access.
- Maximise sequential access.
- Pages are on disk
- Buffer pool is on memory, manage page access.
- DBMS can use memory maping(mmap) to store contents of a file into the address space of a program.
- Don't rely on OS, take care of everythng in DB as knows better
  - Flushing dirty pages to disk in the correcr order
  - Specialized prefetching.
  - Buffer replacement policy.
  - Thread/process scheduling.
- Problems: 
  - transaction safety.
  - i/o stalls
  - error handling.
  - performance issues.
- Main Questions of database storage
  - How the DBMS represents the database in files on disk?
  - How the DBMS manages its memory and moves data back-and-forth from disk?

### File Storage

- stores a db as one or more files on disk typically in a proprietary format to the db.
- OS doesn't know anything about the contents of these files.

### Storage Manager

- responsible for maintaining a database files.
- some do their own scheduling for reads and writes to improve spatial and temporal locality of pages.
- organizes the files as a collection of pages, tracks data read/written to pages and tracks available space.

### Database pages

- Page is a fixed-size block of data.
- can contain tuples, meta-data, indexes and long records but not together, page types not mixed.
- page may be self contained.
- each page is given a unique identifier, indirection layer to map page IDs to physical locations.
- Largest block of data that storage can guarantee failsafe writes. atomicity unit.
- 512B - 16KB
- H/w page - 4kb
- OS page - 4kb too.

### Page storage Architecture

- Heap File Organisation
  - unordered collection of pages with tuples that are stored in random order.
  - support create/ get/ write/ delete page also iteration over pages.
  - maintains special pages that tracks the location of data pages in the database file: page directory.
- Tree File Organisation
- Sequential / Sorted File organisation
- Hashing File Organisation

### Page Structure

- Page header
  - contains metadata about the pages content
  - page size
  - checksum
  - dbms version
  - transaction visibility
  - compression information
  - some systems require pages to self-contained(Oracle).

- Page Layout
  
  - Tuple oriented
    - slotted pages.
    - header keeps track of used slots and offset from the starting location of the last slot used.
    - record ids 
      - keep track of individual tuples
      - each tuple is assigned a unique record identifier, page_id + offset/slot.
      - e.g ctid in postres, file:page:slot in mysql
      - can run Vacuum in postgres to clean tuple pages.
    - Tuple layout
      - sequence of bytes, job of DBMS to interpret those bytes into attribute types and values.
      - prefixed with a header that contain meta-data and attribute data.
      - dont need to store meta-data about the schema.
      - DBMS can physically denormalize related tuples and store them together in the same page. reduce i/o but make updates expensive.
    - Insert a new Tuple
      - check page directory to find a page with a free slot.
      - retrieve the page from disk
      - check slot array to find empty space in page that will fit.
    - Update an existing tuple using record id.
      - check page directory to find location of page.
      - retrieve page from disk.
      - find offset in page using slot array.
      - overwrite existing data(if new data fits)
    - Problems
      - fragmentation
      - useless disk i/o
      - random disk i/o
  
  - Log-structured Storage
    - stores log records that contain changes to tuples(put, delete).
    - each log record must contain tuple's unique identifier
    - put records contain the tuple contents.
    - deletes marks the tuple as deleted.
    - as app makes changes to the db, DBMS appends log records to end of file without checking previous log records.
    - all disk writes are sequential.
    - read a tuple, DBMS finds newest log record corresponding to that id.
    - log-structured compaction.
    - after compaction, DBMS doesnt need to maintain temporal ordering of records within the page.
    - coalesces larger log files into smaller files by removing unnecessary records.
    - DBMS can instead sort the page based on id order to improve efficiency of future look-ups.
    - Sorted String Tables (SSTables).
    - Universal compaction(Rocks DB).
    - Level compaction(Level DB).
    - Problems:
      - Write-amplification??
      - Compaction is expensive.
    
    
  - Variable precision numbers
  - Fixed precision numbers
  - System Catalogs

## Database Workloads

- OLTP - Online Transaction Processing.
  - Fast ops that only read/update a small amount of data each time.
  - usually kind people build first
  
- OLAP - Online Transactional Analytics Processing.
  - Complex queries that read alot of data to compute aggregates

- Hybrid Transaction and Analytical Processing
  - OLAP + OLTP
  
- The relational model does not specify that the DBMS must store all a tuple's attributes together in a single page.
- This may not actually be the best layout for some workloads.

### N-ary storage model (NSM)

- been assuming n-ary storage model aka "row-storage"
- ideal for OLTP.
- fast inserts, updates and deletes
- good for queries that need entire tuples
- not good for scanning large portions of the table and/or a subset of the attributes.

### Decomposition storage model (DSM)

- stores the values of a single attribute for all tuples contiguously in a page, also known as a column store.
- ideal for OLAP workloads where read-only queries perform large scans over a subset of the table's attrbutes.
- tuple identification
  - fixed-length offsets, each value is the same length for an attribute.
  - embedded tuple ids, value is stored with it tuple id in a column.
- advantages: 
  - reduces amount wasted i/o because DBMS only reads data it needs.
  - better query processing and data compression.
- disadvantages:
  - slow for point queries, inserts, updates and deletes because of tuple splitting/stitching.
  
- I/O is the main bottleneck if the DBMS fetches data from disk during query execution.
- DBMS can compress pages to increase the utility of the data moved per I/O operation.
- Key trade-off is speed vs compression ratio.
  - compression reduces the database DRAM requirements
  - It may decrease CPU costs during query execution

- Data sets tend to have highly skewed distributions for attribute values.
- Data sets tend to have high correlation between attributes of the same tuple.


## Database Compression

- Goal 1: Must produce fixed-length values, only exception is var-length stored in separate pool.
- Goal 2: Postpone decompression for as long as possible during query execution, late materialization
- Goal 3: Must be a lossless scheme.  

- Lossless vs Lossy compression(should be at application level).

### Compression Granularity

- Block-level
  - compress a block of tuples for the same table
  - naive compression 
    - LZO, LZ4, Snappy, Zstd.
    - Computational overhead
    - compress vs decompress speed.
  
- Tuple-level
  - compress the contents of the entire tuple(NSM only)

- Attribute-level
  - compress a single attribute within one tuple(overflow)
  - can target multiple attributes for the sam tuple

- Column-level
  - compress multiple values for one or more attrbutes stored for multiple tuples.(DSM only)
  - Run-length encoding
    - compress runs of the same value in a single column into triplets
    - value of the attribute, start position in the column segment, # of elements in run.(v,o,l)
    - sort then compress for better results.
    - requires columns to be sorted intelligently to maximise compression opportunities. 
  - Bit-Packing encoding
    - when values for an attribute are always less than the values declared largest size, store them as smaller data type.
    - mostly encoding - bit-packing variant that uses special marker to indicate when a value excess largest size.
  - Bitmap encoding
    - store a separate bitmap for each unique value for an attribute where an offset in the vector corresponds to a tuple.
  - Delta encoding
    - record the difference between values that follow each other in the same column
    - can combine with RLE for better results
  - Incremental encoding
    - type of delta encoding that avoids duplicating common prefixes/suffixes between consecutive tuples.
    - works best with sorted data.
  - Dictionary encoding
    - build a data structure that maps variable-length values to a smaller integer identifier.
    - replace those values with their corresponding identifier in the dictionary data structure.
      - need to support fast encoding and decoding
      - need to also support range queries.
    - most widely used compression scheme in DBMS.
    - support encode/locate and decode/extract.
  
## Memory management and Buffer Pool

- Spatial Control
  - where to write pages on disk.
  - goal: keep pages used together often as physically close as possible on disk.

- Temporal Control 
  - when to read into memory and when to write them to disk.
  - goal: minimise number of stalls from having to read data from disk.

- Buffer Pool 
  - memory region organized as an array of fixed size pages.
  - an array entry is called a frame.
  - when DBMS requests a page, an exact copy is placed into one of these frames.
  - dirty pages are buffered and not written to disk immediately: write back cache.
  - page table keeps track of pages that are currently in memory.
  - also maintains metadata: dirty flag, pin(prevent swap)/reference counter.
  
### Locks vs Latches
  
- Locks
  - protects the db's logical contents form other transactions
  - held for transation duration
  - need to be able to rollback changes
  
- Latches
  - protects critical sections of the DBMS internal ds from other threads
  - held for operation duration
  - do not need to be able to rollback changes
  
### Page Table vs Page Directory

- Page directory
  - page directory is the mapping from page ids to page locations in the database files
  - all changes must be recorded on disk to allow the dbms to find on restart.

- Page table
  - mapping form page ids to a copy of the page in buffer pool frames
  - in-memory data structure that does not need to be stored on disk
  

## Allocation policies

- Global policies
  - make decisions for all active queries
  
- Local policies
  - allocate frames to a specific queries without considering the behaviour of concurrent queries.
  - still need to support sharing pages.
  
### Buffer Pool Optimizations

- Multiple Buffer Pools
  - per-database buffer pool.
  - per-page type buffer pool
  - partitioning memory access across multiple pools helps reduce latch contention and improve locality
  - approach:
    - object id.
    - hashing.
    
- Pre-Fetching
  - can prefetch pages based on a query plan: sequential scans, index scans. 
  
- Scan Sharing
  - reuse data retrieved from storage or operator computations
  - also called synchronized scans.
  - different form result caching.
  - cursor sharing.

- Buffer Pool Bypass
  - sequential scan operator will not store fetched pages in the buffer pool to avoid overhead.
  - memory is local to running query
  - works well if operator needs to read a large sequence of pages that are contiguous on disk
  - store temporary data.
  - light scans

- OS page cache
  - most disl ops go through the OS API.
  - unless DBMS tells it not to, the OS maintains its own filesystem cache(page cache, buffer cache)
  - most DBMS use direct i/o(o_direct) to bypass the OS's cache.
    - redundant copies of pages
    - different eviction policies
    - loss of control over file i/o.

### Buffer replacement policies

- when DBMS needs to free up a frame to make room for a new page, it must decide which page to evict from buffer pool.
- goals:
  - correctness
  - accuracy
  - speed
  - meta-data overhead
- Least Recently Used
  - maintain a single timestamp of when each page was last accessed.
  - when eviction comes, select the one with the oldest timestamp.
  - keep pages in sorted order to reduce the search time on eviction.
- Clock 
  - approximation of LRU that does not need a separate timestamp per page.
  - each page has a reference bit, when accessed set to 1.
  - organize pages ina circular buffer with a clock hand
  - upon sweeping, check if a page bit is set to 1, if yes, set to zero, if no, evict.
  - *common mechanism* - read more.
- Clock and LRU are susceptible to sequential flooding.
- Better policies: 
  - LRU-K
    - track history of last K references to each page as timestamps and compute the interval between subsequent accesses.
    - DBMS then uses history to estimate the next time that page is going to be accessed.  
  - Localization
    - chooses which pages to evict per query basis
    - minimizes pollution of the buffer pool from each query.
    - postgres maintain small ring buffer that is private to the query.
  - Priority Hints
    - DBMS knows about the context of each page during query execution
    - provides hints to the buffer pool on whether a page is important or not.

### Dirty pages

- Fast Path:
  - if a page in the buffer pool is not dirty then the DBMS can simply drop it.

- Slow Path
  - if a page is dirty then the DBMS must write back to disk to ensure that its changes are persisted.

- Background writing
  - DBMS can periodically walk through the page table and write dirty pages to disk
  - when dirty page is written, DBMS can either evict or unset dirty flag.
  - careful system doesnt write dirty pages before their log records are written.
  
### Other Memory Pools

- DBMS needs memory for things other than indexes and tuples
- May not be always backed by disk
- e.g:
  - Sorting + Join buffers
  - Query caches.
  - Maintenance buffers
  - Log buffers
  - Dictionary caches.

- DBMS can almost always manage memory better than the OS.
- Leverage semantics about the query plan to make better decisions.


## Hash Tables

- How to support the DBMS execution engine to read/write data from pages.
- Data structures:
  - Hash Tables
  - Trees

- Use cases:
  - Internal Meta-data
  - Core data storage
  - Temporary data structures  
  - Table Indexes.
  
- Design decisions
  - Data Organisation
    - how we layout data structure in memory/pages and what information to store to support efficient access.
  - Concurrency
    - how to enable multiple threads to access the data structure at the same time without causing problems.

- Hash Table
  - implements an unordered associative array that maps keys to values.
  - uses a hash function to compute an offset into this array for a given key, from which the desired value can be found.
  - space complexity o(n), Time complexity: average O(1), worst O(n)
  
  - Static Hash Table:
    - allocate a giant array that has one slot for every element you need to store.
    - to find an entry, mod the key by the number of elements to find offset in the array.
    - Assumptions:
      - number of elements is known ahead of time and fixed.
      - each key is unique
      - perfect hash function
  
  - Design Decisions:
    - Hash Function
      - how to map a large key space into a smaller domain
      - trade-off between being fast vs collision rate.
    - Hashing Scheme
      - how to handle key collisions after hashing
      - trade-off between allocating a large hash table vs additional instructions to get/put keys.
  
  - Hash Functions
    - For any input key, return an integer representation of that key.
    - We dont want to use a cryptograhic hash function for DBMS hash tables
    - desire fast and low collision rates
    - Namely:
      - CRC-64 (1975) - used in networking for error detections
      - MurmurHash (2008) - fast, general purpose hash function
      - Google CityHash (2011) - faster for shorter keys
      - *FB XXHash (2012) - creator of zstd compression
      - Google FarmHash (2014) - newer version of CityHash with better collision rates
      
  - Static Hashing Schemes
    - *Linear Probe Hashing
      - also known as open addressing
      - single giant table of slots
      - resolve collisions by linealry searching for the next free slot in the table.
      - inserts and deletions are generalizations of lookups.
      - Non-unique Keys
        - Separate Linked List
        - Redundant Keys
        
    - Robin Hood Hashing
      - Variant of linear probe hashing that steals slots from rich keys and give them to poor keys.
      - difference from initial position and move the rest to equidistant position.
    
    - Cuckoo Hashing
      - use multiple hash tables awith different hash function seeds.
      - one insert, check every table and pick anyone that has a free slot, if no table has a free slot, evict element from one of them and re-hash it to find a new location.
      - look-ups and deletions are always because only one location per hash table is checked.
    
- *above hash tables require the DBMS to know the number of elements it wants to store, otherwise needs to rebuild the table if it needs to grow.shrink in size*.
  
  - Dynamic Hashing Schemes
    - Chained Hashing
      - maintain a linked list of buckets for each slot in the hash table.
      - resolve collisions by placing all elements with the same hash key into the same bucket.
    - Extendible Hashing 
      - multiple slot locations can point to the same bucket chain
      - reshuffle bucket entries on split and increase the number of bits to examine.
    - Linear Hashing
      - maintains a pointer that tracks the next bucket to split
        - when bucket overflows, split the bucket at the pointer location
      - use multiple hashes to find the right bucket for a given key.
      - can use different overflow criterion
        - space utilization.
        - average lenght of overflow chains.
      - splitting buckets based on the split pointer will eventually get to all overflowed buckets. 
  
  - B+ Trees
    
    - This is a self-balancing tree data structure that keeps data sorted and allows searches, sequential access, insertions and deletions always in 0(log n)
    - Generalization of a binary search tree, since a node can have more than two children.
    - optimized for systems that read and write large blocks of data.
    - mostly used for table indexes
    - A tableindex is a replica of a subset of a table's attributes that are organised and/or sorted for efficient access using those attributes.
    - DBMS ensure that contents of the table and the index are logically synchronized.
    - It's the DBMS job to figure out the best indexes to use to execute each query.
    - There is a trade-off regarding the number of indexes to creaeper database
      - storage overhead
      - maintenance overhead
    - *the ubiquitous b-tree*, *efficient locking for concurrent ops on b-trees*
    
    - Properties: 
      - M-way search tree.
      - its perfectly balanced, every leaf node is at the same depth in the tree.
      - every node other than the root is at least half-full.(M/2-1 < #keys < M-1)
      - every inner node with k keys has k+1 non-null children
    
    - Structure
      - comprised of an array of key/value pairs
      - keys are derived from the attributes that index is based on
      - values will differ based on whether the node is classified as an inner node or a leaf node.
      - arrays are kept in sorted key order, usually.
      - leaf node values
        - record IDs
          - a pointer to the location of the tuple to which the index entry corresponds
        - tuple data
          - leaf nodes store the actual contents of the tuple
          - secondary indexes must store the record ID as their values.
      - original b-tree stored jeys and values in all nodes in the tree
      - b+ tree only stores values in leaf nodes, inner nodes only guide search.
      - consistent with implementation
      - duplicate keys
        - Append record ID.
        - Overflow leaf nodes  
      
      - Clustered Indexes
        - table is stored in sort order specified by the primary key.
        - can either heap or index organized storage.
        - some DBMS always use a clustered index.
          - if a table doesnt contain primary key, DBMS will automatically make a hidden primary key.
        - other DBMS cannot use them at all.
        - clustered B+ tree
      
      - Index scan page sorting
        - retrieving tuples in the order the appear in a non-clustered index due to redundant reads
        - the DBMS can first figure out all the tuples that it needs and then sort them based on the page ID.
        
      - Covering Indexes
      - Design Choices