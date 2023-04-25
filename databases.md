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
    - 