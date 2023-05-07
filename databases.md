# Databases 

Ref:
`Jamie Brandon What is a database TigerBeetle talk`
`Database Engineering by Meta`
`Expert Mysql`
`Database System Concepts 7th Edition`
`CMU: Intro to Databases Systems`
`Mysql for Developers: Aaron@planetscale.com`


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
- DBMS 
  - software that allows applications to store and analyze information in a database.
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

## MYSQL SERIES

### SCHEMA

- Choose smallest data type for your data.
- Schema should be honest and represent your data.
  - Integers
    - TINYINT, SMALLINT, MEDIUMINT, INT, BIGINT.
    - Stored in increasing 1 byte apart from BIGINT which is 8 bytes.
    - Should also specify signed or unsigned.
    - Number in bracket does nothing to size.

  - Decimals
    - Store numbers with decimal parts
    - DECIMAL(exact), FLOAT(approximation)
    - decimal(10,4)
    - double is bigger than float, 8 to 4 bytes.
  
  - Strings
    - CHAR, VARCHAR, BINARY, VARBINARY, BLOB, TEXT, ENUM, SET.
    - CHAR(fixed length), VARCHAR(variable length)
    - keep eye on collations when choosing character sets.
    - BINARY mainly used to store hashes.
    - TEXT, BLOB - large amount of data in character or binary format.
    - Only select them when you need them.
  
  - Enums
    - range of values a column can take.
    - sorts by enum position integer starting at 1.
    
  - Dates
    - DATE(3), DATETIME(8), TIMESTAMP(4), YEAR(1), TIME(3).
    - TIMESTAMP - (1970 - 2038-01-19)
    - set session timezone.
    - TIMESTAMP preffered when it comes to storing and retrieving timezone sensitive data.
    - CURRENT_TIMESTAMP attribute on column, on update too.
  
  - JSON
    - Mysql validates json.
    - Special functions for working with json.
    - can't index on entire json document.
  
  - Unexpected types
    - Booleans sameas tinyint
    - Zip codes char(10)
    - IpAddress - function to work with ip-addresses. check them out.
  
  - Gnerated columns
    - use AS(function to obtain value form another column)
    - better than relying on application code to handle this
    - read more on this.
    - has to be deterministic.
    - Stored vs Virtual.
  
  - Schema migrations
    - maintanable, sharable chnage to schema
    
```sql

TINYINT    - 1  - -128
SMALLINT   - 2  - -32768
MEDIUMINT  - 3  - -8388608
INT        - 4  - -2147483648
BIGINT     - 8  - -2^63

```

### INDEXES

- A separate data structure that maintains a copy of part of your data and points back to row.
- Create as many as you need but as few as you can get away with.
- Queries should drive the number and kind of indexes you need.

  - B+ Tree
    - most common index ds.
  
  - Primary Keys
    - Indexes are automatically created for primary keys.
    - Always no nullable.
    - Unsigned, Auto_increment.
    - One per table.
    - determine how table is stored on disk, is the table.
    - clustered index.
  
  - Secondary keys
    - Any index that is not the primary key.
    - Secondary index has pointer to the primary index.
  
  - Primary Key data types
    - have room to grow.
    - keep an eye on size of index.
    - unsigned bigints
    
  - Where to add indexes
    - Effective indexes for each query.
    - EXPLAIN keyword to get a glimpse into how query is run.
    - use EXPLAIN to see which indexes are used per query.
    - order, group, ranges, bounded ranges
  
  - Index selectivity
    - cardinality, 
    - column with few distinct values not perfect candidate for indexes.
  
  - Prefix indexes
    - possible to create an index on only a portion of a column.
    - use selectivity off the full index to determine how close you can get to original index selectivity by just indexing part of column.
    - can't be used to sort
  
  - Composite index
    - indexes on multiple columns
    - order of definiton crucial to query performance.
    - access is left-to-right, no skipping, stops at first range
    - key-len column on index table.
    - access patterns crucial to consider when defining ci.
  
  - Covering Indexes
    - is a regular index in a special situation.
    - when an index supplies all a given query requires.
  
  - Functional Indexes
    - Index on a function, index something you can't quite reach.
    - when you wrap a column in a function, MYSQL has no idea what's in there, blackbox essentially.
    - case of index obsufication.
    - *case sensitive collation* works well for comparison.
    - `alter table users add index m((month(birthday)))`
    - Mysql: 8 and beyond, 5 : use generated column index
    
  - Indexing JSON columns
    - No way to index an entire JSON column in MySQL
    - `json ->> '$.email'` generated column, unquoting operator
    - Careful with collation here even after CAST.
    
  - Indexing for wildcard searches
    - when searching for substring in a column, indexes can still be used but only unitl the first wildcard.
    - 
  
  - Fulltext Indexes
    - Mysql supports Fulltext searching although not as good as purpose built FTS tools.
    - add fulltext keyword
    - syntax match(a,b,c) against (expression)
    - BOOLEAN mode and NORMAL LANGUAGE mode
    - relevancy score
    
  - Invisible indexes
    - Before you drop an index, you cna make an index invisible to test effects of removing it.
  
  - Duplicate indexes
    - Because of the way composite indexes work, you may have duplicate and redundant indexes defined.
    
  - Foreign Keys
    - Not the same as foreign key constraints
    - Allows one to reference data in another table.
    - Constraints check for referential integrity
    - column types have to be the same type.
    - on delete cascade/set null, on update cascade/set null.
    - constraint in application code.
    

### QUERIES
    
  - EXPLAIN
    - Learn to read and interpret the EXPLAIN statement.
  
  - Explain access type
    - Const - unique look up
    - Ref - index use
    - range - equality
    - index - scan entire index
    - all - scan entire table.
    - look them up in documentation.
  
  - Explain Analyze
    - gives more detail to work with as compared to traditional EXPLAIN output.
    - can change format i.e format=tree, format=json
    - explain analyze runs the query
  
  - Index Obfuscation
    - happens when you wrap a column with a function.
    - always work on the right side of the query to avoid this, leave column alone.
  
  - Redundant and Approximate conditions
    - Impossible to index a condition correctly.
    - Use a redundant condition to help narrow down records quickly.
    - should be logically correct.
    
  - Select only what you need.
    - especially for large data format fields.
    - check ActiveRecord model for hrams of this.
    - invisible keyword to exclude from default SELECT * unless explicitly called.
 
  - Limiting rows
    - onyl return rows to remain performant.
    - look for calculations or operations that can be done in the DB.
    - slect count(*) and select *
    - accompany your LIMIT with ORDER BY.
    
  - Joins
    - `select * from users(left table) join locations(right table) on users.id=locations.manager_id;`
    - default is inner join(left matches right and has results on both)
    - left join(all results on left table)
    - right join(all results on right table)
  
  - Indexing Joins
    - combining joins efficiently.
    - *relook at this

*chapter 8 of MySql documentation on optimization*

  - Subqueries
    - can be used in place of joins.
    - used to either include or remove results of a query based on another query,
    - semi-join, anti-join
    - where exists, where not in,
    
  - Common Table Expressions(CTEs)
    - powerful way to refactor complicated queries into more readbale versions.
    - with table_name as (insert query)
    - run only once.
  
  - Recursive CTEs
    - CTE that references itself 
    - can be used to generate new data or work with existing data.
    - use recursive keyword.
  
  - Unions
    - puts the results one on top of the other as opposed to joins which match them.
    - union all ignores duplicates
    - 
  
  - Window Functions
    - Allow you to work on a subset of rows related to the current row while query is being processed.
    - row_number() over()
    - produce ranked lists, 
    - named windows
    - maintains individual rows even after processing
  
  - Sorting and Limiting
    - order by....default asc
    - limit ... not deterministic.
  
  - Sorting with indexes
    - don't sort your rows if you dont need them sorted.
    - either via index or after results have been returned(filesort).
    - can create a desc index.
    
  - Sorting with composite indexes
    - order remains ad definition.
    - index and query have to be the same.
  
  - Counting results
    - count(*) - count as fast as you can.
    - include a function inside count function
    
  - Dealing with NULL
    - use <=> operator to get NULL out of column.
    - also called null safety operator.
    - ifnull(), coalesce(preffered, default, fallback)

### MISC EXAMPLES

  - MD5 Hash
    - create a very fast lookup on very large values.
    - can work on multiple columns
    - GENERATED ALWAYS AS()
    - good for strcit equality and constraint esp on logical units across columns
  
  - Bitwise Operations
    - use the 8 bits to represent different states i.e flags
    - implementation side vs interface side.
    - *very cool stuff*
  
  - Timestamps versus booleans
    - when storing a boolean it is sometimes desirable to know when it was turned on/off.
    - timestamp null
    - *look more*
    
  - Claiming rows
    - application needs to claim rows for processing.
    - set owner
    
  - Summary table
    - also known as rollup table.
    - (*)
    
  - Meta Tables
    - wide tables with many columns can be expensive to query and maintain.
    - makes sense to put them in a secondary table.
    - blob, json ,text columns for example.
    
  - Offset limit pagination
    - has to be deterministic.
    - 
    - Offset/limit method most common.
    - pages may drift as they are looking at them.
    - formula = page * offset.
    - to show pages have to know how many they are.
    - next button reliant on whether an extra record exists.
  
  - Cursor pagination
    - keep track of some record of state...i.e id, token.
    - can't directly address a page
    - great for infinite scroll.
    - anything you order by should be in cursor
  
  - Deferred Joins
    - offset/limit performant as you reach deeper pages.
    - 
  
  - Geographic searches
    - ST_DISTANCE_SPHERE function
    - bouding box of a point.
    
    
      


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
  - query tree 
    - turned from a logical plan to physical plan.
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
      - Node size 
        - the slower the storage device the larger the optimal node size of a B+ tree.
        - optimal sizes can vary depending on the workload.
        - leaf node scans vs root-to-leaf traversals
      - Merge Threshold
      - Variable-length keys
        - Pointer
        - Varibale lenght nodes
        - Padding
        - Key Map /Indirection 
      - Intra-node search
        - Linear
          - use SIMD to vectorize comparisons
        - Binary
          - jump to middle, pivot left/right depending on comparison
        - Interpolation
          - approx location of desired key based on known distribution of keys.
      
      - Optimizations
        - Prefix compression
        - Deduplication
        - Suffix Truncation
        - Pointer Swizzling
        - Bulk Insert
        - Buffer Updates
    
  ## Concurrent Control
   
  - Assumption has been all DS that we've discussed so far are single-threaded, but a DBMS needs to allow multiple threads to safely access DS to take advantage of CPU core and hide I/O disk stalls.
  - Concurrency control protocol is the mehtod that DBMS uses to ensure correct results for concurrent operations on a shared object
  - Protocol correctness criteria can vary:
    - Logical correctness: can thread see data it is not supposed to see.
    - *Physical correctness: is IR of the object sound
  - Locks vs Latches
  
  - Latch Modes
    - Read Mode
    - Write Mode  
  
  - Latch Implementations
    - Blocking OS mutex.
    - Reader-writer latches
  
   - Hash Table Latching
    - Page Latches
    - Slot Latches  
    
  - B+ Tree concurrency control
    - multiple threads to read and update the b+ tree.
    - Latch crabbing/coupling
      - allow access/modify
      - latch parent, latch child, release if parent is safe.
      - a safe node is one that will not split or merge on update
      - fifo release style.
      - better latching algorithm
    - Leaf noe scanning concurrency
    - Latches do not support deadlock detection or avoidance, only via coding discipline
*Making a data structure thread-safe is notoriously difficult in practice*

> OPERATOR EXECUTION

## Sorting and Aggregation Algorithms

- Query Plan
- Disk-oriented DBMS.
- Still need the buffer pool.
- if fits in memory, quicksort

- Include: 
  - Top N Heap Sort
    - if a query contains an order by with a limit, DBMS only needs to scan data once to find top-N els.
    - scan data once and maintain an in-memory sorted priority queue.
  - External Merge Sort
    - Divide and conquer algo that splits data into separate runs, sorts them individually and then combines then into longer sorted runs.
    - Sorting
      - sort chunks of data that fit in memory and then write back the sorted chunks to a file on disk.
    - Merging
      - combine sorted runs into larger chunks.
    - a run is a list of key(attr to compare)/value(tuple(early mat), record ID) pairs.
    - 2-way external merge sort.
  - B+tree to sort
    - clustered b+ tree.
  - Aggregations
    - collapse values for a single attribute from multiple tuples into a single scalar value.
    - DBMS needs to quickly find tuples with the same distinguishing attributes for grouping.
    - Implementations:
      - Sorting
        - partition.
        - rehashing
      - Hashing

## Join Algorithms

- We normalize tables in a relational database to avoid unnecessary repetition of information.
- We then use the join operator to reconstruct the original tuples without loss of information.
- We will focus on performing binary joins using inner equijoin algorithms
  - can be tweaked to support other joins.
  - multi-way joins exist but only in research
- In theory we want the smaller table to always be the left table in the query olan
  - optimizer will figure this out when generating the physical plan.
- Join Operators
  - Output
    - what data does the join operator emit to its parent operator in the query plan tree.
    - output contents can vary:
      - processing model
      - storage model
      - data requirements in the query.
  - Cost Analysis Criteria
    - how do we determine whether one join algorithm is better than the other.
    - Number of IOs to compute join
- Join Algorithms
  - Nested Loop Join
    - Simple/Stupid
      - Nested loop join
    - Block
      - for every block in R scans S once.
    - Index
  - Sort-Merge Join
    - Sort
      - sort both tables on the join key
      - worst case is when join attribute of all tuples is same on join tables
    - Merge
  - Hash Join
    - Build
    - Probe
    - Cost Analysis
    - Optimization
      - Probe filter
        - Bloom Filter
          - probabilistic data structure that answers set membership queries
            - false negatives will never occur
            - false positives can sometimes occur
    - Partitioned Hash Join
      - Hash join when tables dont fit in memory
        - Bloom Filter
          - probabilistic data structure that answers set membership queries
            - false negatives will never occur
            - false positives can sometimes occur
    - Partitioned Hash Join
      - Hash join when tables dont fit in memory.

*Integrate a profile for project*
## Query Execution
 
  - Processing Models
    - defines how the system executes a query plan
    - different trade-offs for different workloads
    
    - Approaches:
      - Iterator Model
        - also known as volcano or pipeline model.
        - each query olan operator implements a Next() function.
        - used in almost every DBMS, allows for tuple pipelining.
        - some operators must block until their children emit all their tuples, joins, subqueries, order by.
      - Materialization Model
        - Each operator processes its input all at once and then emits its output all at once.
        - Better for OLTP workloads because queries only access a small number of tuples at a time. 
        - Not good for OLAP with large intermediate results.
      - Vectorized / Batch Model
        - each operator emits a batch of tuples instead of a single tuple
        - ideal for OLAP / data warehouses because it greatly reduces the number of invocations per operator.
        - allows for opearators to more easily use SIMD to process batches of tuples.
  
  - Plan Processing Direction
    - Top-to-Bottom
      - start with the root and pull data up from its children
      - tuples are always passed with function calls.
    - Bottom-to-Top
      - start with lead nodes and push data to their parents
      - allows for tighter control of caches/registers in pipelines.
  
  - Access Methods
    - This is the way DBMS access the data stored in a table
    - Approaches
      - Sequential
        - Optimizations
          - Prefetching
          - Buffer Pool Bypass
          - Parallelization
          - Heap Clustering
          - Late Materialization
          - Data Skipping
            - Approximate Queries (Lossy)
              - execute queries on a sampled subset of the entire table to produce approxiate results
            - Zone Maps (Loseless)
              - pre-computed aggregates for the attribute values in a page. DBMS checks zone map first to decide whether it wants to access the page
      - Index Scan
        - DBMS picks an index to find the tuples that the query needs.
        - Depends on:
          - What attributes the index contains
          - What attributes the query references
          - Attribute's value domains
          - Predicate composition
          - Whether the index has unique or non-unique keys
      - Multi-Index Scan
        - multiple indexes that the DBMS can use for a query.
  - Modification Queries
    - operators that modify the database(INSERT, UPDATE, DELETE) are responsible for modifying the target table and its indexes.
      - constraint checks can either happen immediately inside of operator or deferred until later in query/transaction.
    - Halloween problem
      - anomaly where an update operations chages the physical location of a tuple which causes a scan operator to visit the tuple multiple times.
      - track modified record ids per query.
      
  - Expression Evaluation
    - represents a WHERE clause as an expression tree.
    - nodes in the tree represent different expression types
      - comparisons, conjuction, arithmetic operators, constant values, tuple attribute references.
  
  - JIT compilation can potentially speed times up.
  
## Parallel Query Execution

- Multiple workers in the same database
- Increased perfomance for potentially the same hardware resources
  - Higher Throughput
  - Lower Latency
- Increased responsiveness of the system.
- Potentially lower TCO(total cost of ownership)
  - fewer machines
- Parallel vs Distributed
  - both appear as single logical to a client application or DBMS frontend.
  - same wury should have the same results for both.
  - resources close vs far from each other
  - resources communicate fast vs slow.
  - comminication cheap and reliable vs very opposite.
- Process Models
  - how system is architectured to support concurrent requests ftom a multi-user application
  - worker is the DBMS component that is responsible for executing tasks on behalf of the client and returnin the results.
  - Approach:
    - Process per DBMS worker
      - each process is a separate OS process.
      - relies on OS scheduler.
      - uses hared memory for gloabl data structures.
    - Thread per DBMS worker
      - single process with multiple worker threads.
      - DBMS manages its own scheduling
      - may or not use a dispatcher thread.
      - thread crash may kill the entire system.
      - Scheduling
        - How many tasks should it use?
        - How many CPU cores should it use.
        - What CPU core should the tasks eecute on
        - Where should a task store its output
      - DBMS always knows more than the OS.
      - *SQLOS* 
        - user-level OS layer that runs inside the DBMS and manages provisioned hardware resources.
        - Non-preemptive thread scheduling through instrumented DBMS code.
    - Embedded worker
      - runs inside the same address space as the application
      - application is responsible for threads and scheduling
      - application may support outside connections, BerkeleyDB, SQLite, RocksDB, LevelDB.
    - Process models
      - adavantages of multi-thread
        - less overhead per context switch
        - do not have to manage shread memory.
        - thread per worker does not mean that DBMS supports intra-query parallelism
    - Inter vs Intra Query Parallelism
      - Inter: Execute multiple disparate queries simultaneously
        - increases throughput and reduces latency
        - if read-ony then this requires almost no explicit co-ord between queries.
        - hard if updating tables.
      - Intra: Execute the operations of a single query in parallel
        - improve the performance of a single query by executing its operators in parallel.
        - there are parallel versions of every operator.
        - decreases latency for long-running queries esp for OLAP queries.
        - Approaches
          - Intra-operator(Horizontal)
            - inserts an exchange operator into the query plan to coalesce/split results from multiple parent/child.
            - types: Gather, Distribute, Repartition
          - Inter-operator(Vertical)
            - also called pipeline parallelism.
          - Bushy
            - Hybrid of Intra and Inter
            
    - Using additional processes/threads to execute queries in parallel wont help if the disk is always the main bottleneck.
- Execution Parallelism
- I/O Parallelism
  - split the DBMS across multiple storage devices to improve disk bandwidth latency.
  - many different options that have trade-offs
    - multiple disks per db.
    - one db per disk.
    - one relation per disk.
    - split relation across multiple disks
  - some dbms support this natively, others require admin to configure outside of DBMS.
- Database Partitioning
  - some DBMS allow you to specify the disk location of each individual database.
  - *discussed further below*
- Issues:
  - Coordination Overhead.
  - Scheduling
  - Concurrency Issues
  - Resource Contention.
  
## Query Planning and Optimization

- For a given query find the correct execution plan that has the lowest "cost".
- This is the part of a DBMS that is the hardest to implement well(proven to be NP-complete)
- No optimizer truly produces the "optimal" plan.
  - Use estimation techniques to guess real plan cost
  - Use heuristics to limit the search space.

- **This is the hardest part of any database**

- Logical plans vs Physical plans
  - the optimzer generates a mapping of a logical algebra expression to the optimal equivalent physical algebra expression
  - Physical operators define a specific execution strategy using an access path.
    - they can depend on the physical format of the data that they process(sorting, compression).
    - Not always a 1:1 mapping from logical to physical.
  - The goal is to increase the likelihood of enumerating the optimal plan in the search.
  
- Architecture overview
  - | Application | --> SQL Query -> | SQL Rewriter | --> SQL Query -> | Parser | --> AST -> | Binder | --> Logical Plan | Tree Rewriter | --> Logical Plan -> | Optimizer | --> Physical Plan | Execute |.
  - System Catalog --> name - internal ID, Schema Info, 
  - Cost model --> Estimates.

- Query Optimization
  - Heuristics / Rules
    - rewrite the query to remove stupid / inefficient things
    - techniques may need to examine catalog but they do not need to examine data
  - Cost based search
    - use a model to estimate the cost of executing a plan.
    - enumerate multiple equivalent plans for a query and implement the one with the lowest cost

- Logical Query Optimization
  - Split Conjuctive predicates
    - decompose predicates into their simplest forms to make it easier for the optimizer to move them around.
  - Predicate pushdown
    - move the predicate to the lowest applicable point in the plan.
  - Replace cartesian products with joins
    - replace with inner joins using the join predixcate
  - Projection pushdown  
    - eliminate redundant attributes before pipeline breakers to reduce materialization cost.

- Nested Sub-Queries.
  - DBMS treats nested sub-queries in the where clause as functions that take parameters and return a single value or set of values
  - Two approaches
    - Rewrite to de-correlate and/or flatten them
    - Decompose nested query and store result to temporary table.
      - for harder one, optimzer breaks up queries into blocks and then concentrates on one block at a time.
      - sub-queries are written to a temporary table that are discarded after the query finishes

- Expression Rewriting 
  - Optimizer transforms a query expressions(WHERE/ON) into the minimal set of expressions.
  - Implemented using if/then/else clauses or a pattern-matching rule engine.
    - search for expressions that match a pattern
    - when a match is found, rewrite the expression
    - Halt if there are no more rules that match.
  - Impossible /Unnecessary predicates
  - Merging predicates

- Cost Estimation
  - DBMS uses a cost model to predict the behavior of a query plan given a database state
    - Internal cost that allows the DBMS to compare one plan with another.
  - Its too expensive to run every possible plan to determine this information, so DBMS need a way to derive this information.
  - * Look into MongoDB implementation 
  - Cost model components:
    - Physical costs
      - predict CPU cycles, I/O, cache misses, RAM consumption, network messages
      - heavily dependant on hardware
    - Logical Costs
      - estimate output size per operator
      - independent of the operator algorihtm
      - need estimations for operator result sizes
    - Algorithmic costs
      - Complexity of the operator algorithm implementation
  - Postgres cost model
    - uses a combo of CPU and I/O costs that are weighted by magic constant factors.
    - default settings are obviously for a disk-resident database without a lot of memory
      - processing a tuple in memory is 400x faster than reading a tuple from disk
      - sequential i/o is 4x faste than random i/o.
    - some dbs run benchmark tests to update planner cost constants.
  - Statistics
    - DBMS stores internal statistics about tables, attributes and indexes in its internal catalog
    - Different systems update them at different times
    - Manual invocations
      - Postgres/SQLite: ANALYZE
      - Oracle/MySQL: ANALYZE TABLE
      - SQL Server: UPDATE STATISTICS
      - DB2: RUNSTATS
    - Selection Cardinality
      - Uniform Data
        - distribution of values is the same.
      - Independent Predicates
        - predicates on attributes are independent
      - Inclusion Principle
        - domain of join keys overlap such that each key in inner relation will also exist in the outer table
    - Correlated attributes
    - Statistics storage:
      - Histograms
        - maintain an occurence count per value (or ranhe of values) in a column
        - equi-width histogram.
        - equi-depth histogram.
      - Sketches
        - probabilistic data structure that gives an approximate count for a given value
        - can replace histograms with sketches to improve its estimate accuracy
      - Samplings
        - maintains a small subset of each table that is used to evaluate expressions to compute selectivity.
        - update samoles when the underlying table chages significantly.
        
  - Query Optimization
    - After performing rule-based rewriting, the DBMS will enumerate different plans for the query and estimate their costs
    - I.e: 
      - Single relation
      - Multiple relations
      - Nested sub-queries
    - Chooses the best plan it has seen for the query after exhausting all plans or some timeout.
    - Single relation
      - Pick the best access method
      - Predicate evaluation ordering
      - Simple heuristics are often good enough for this.
      - OLTP queries are easy
        - Sargable (Search Argument Able)
    - Multiple Relation QP
      - Bottom-up optimization
        - use static rules to perform initial optimization.
        - The use dynamic programming to determine the best join order for tables using the divide and conquer search method.
        - Used by most DBMS
        - System R optimizer
      - Top-down optimization
        - Start with a logical plan of what we want the query to be.
        - Perform a branch and bound search to traverse the plan tree by converting logical operators into physical operators
          - keep track of global best plan during search
          - treat physical properties of data as first class entities during planning.
        - **Watch the MSSQL query optimizer talk**
        
## Concurrency Control Theory

  - A DBMS's concurrency control and recovery components permeate throughout the desing of its entire architecture.    
  - Motivation:
    - How to avoid race condition? Lost Updates
    - What is the correct database state? after a failure? Durability
  - Concurrency control and recovery
    - valuable properties of DBMS
    - Based on concept of transactions with ACID properties

### Transactions

  - A Tx is the execution of a sequence of one or more operations on a database to perform some higher-level function
  - It is the basic unit of change in a DBMS
    - partial tx are not allowed.
  - Strawman system
    - execute txn one-by-one as they arrive at the DBMS.
    - before a txn starts, copy the entire db to a new file and make all changes to that file.
    - is it correct? is it fast?
  - A better approach is to allow concurrent execution of independent txns
    - better utilization/throughput
    - increased response times to users
  - Arbitrary interleaving of operations can lead to"
    - Temporary Inconsistency
    - Permanent Inconsistency
  - A DBMS is only concerned about what data is read/written from /to the database.
  - Transactions in SQL
    - start with BEGIN command
    - Stops with either COMMIT or ABORT.
  - Atomicity, Consistency, Isolation, Durability...ACID.
  - Atomicity
    - Txn always either executes all its actions or executes no actions at all.
    - Approach:
      - Logging
        - logs all actions so that it can undo the actions of aborted txns
        - maintain undo records both in memory and on disk
      - Shadow Paging
        - makes copies of pages and txns make changes to theose copies, only when the txn commits is the page made visible to others
  - Consistency
    - world represented by the db is logically correct, all qiestions asked about the data are given logically correct answers
    - Database consistency
      - accurately models the real world and follows integrity constraints
      - txns in future see the effects of txns committed in the past inside of the database
    - Transaction consistency
      - if db is consistent before txn starts it will also be consistent after
      - this is the application's responsibility.
  - Isolation
    - usr submit txns and each txn executes as if it was running by itself
    - but the dbms achieves concurrency by interleavinf txns
    - A concurrency control protocol is how the DBMS decides the proper interleaving of operations from multiple transactions
    - Categories:
      - Pessimistic - dont let problems arise in the first place.
      - Optimistic - assume conflicts are rare and deal with them after they happen.
    - When one txn stalls because of resource then another txn allowed to go forward.
    - Formal properties of schedules
      - Serial schedule
        - a schedule that does not interleave the actions of different transactions
      - Equivalent schedules
        - for any db state, the effect of executing the first schedule is identical to the effect of executing the second schedule.
      - Serializable schedule
        - a schedule that is equivalent to some serial execution of the transactions
        - if each txn preserves consistency, every serializable schedule preserves consistency
        - Serializability is a less intuitive notion of correctness compared to txn initiation time or commit order, but it provides the DBMS with more flexibility in scheduling operations
        - more flexibility means better parallelism.
    - Conflicting operations
      - need a formal notion of equivalence that can be implemented efficiently based on the notion of conflicting operations
      - Two ops conflict if:
        - they are by different transactions
        - they are on the same object and one of them is a write.
      - Interleaved Execution Anomalies
        - Read - Write conflicts
        - Write- Read conflicts
          - one txn reads data written by another txn that has not committed yet
        - Write - Write conflicts.
          - one txn overwrites uncommitted data from another uncommitted txn
    - Levels of serializability
      - Conflict serializability*
      - View Serializability - no system can do this?????
    - Conflict serializable schedules
      - Two txns are conflict equivalent iff:
        - they involve the same actions of the same txns
        - every pair of conflicting actions is ordered the same way.
      - Schedule S is conflict serializable if:
        - S is conflict equivalent to some serial schedule
        - Intuition
    - Dependency graph
  - Durability
    - all changes of committed txns should be persistent
      - no torn updates
      - no changes from failed transactions
    - DBMS can use either logging or shadow paging  to ensure all changes are durable
  - Concurrency control and recovery are among the most important functions provided by a DBMS
 
  ## Two Phase Locking
  
  - We need a way to guarentee that all execution schedules are correct(serializable) without knowing the entire schedule ahead of time.
  - Use *LOCKS* to protect database objects.
  - Existing component called a Lock Manager.
  - Locks: 
    - Separate User transactions.
    - Protect Database contents.
    - During Entire Transactions
    - Modes: Shared, Exclusive, Update, Intention
    - Deadlock Detection and Resolution
    - By Waits-for, Timeout, Aborts
    - Kept in Lock Manager.
  - Basic Lock Types
    - S-LOCK: Shared locks for reads.
    - X-LOCK: Exclusive locks for writes.
  - Executing with locks
    - Transactions request locks
    - Lock manager grants or blocks requests
    - Transactions release locks
    - Lock manager updates its internal lock-table.
      - keeps track of what transactions hold what locks and what transactions are waiting to acquire any locks.
      - global view of a transactions activity
      - cheaper than updating latches on a B+ TREE.
  - Concurrency control protocol
    - 2PL is a concurrency control protocol that determines whether a txn can access an object in the database at runtime.
    - protocol does not need to know all the queries that a txn will execute ahead of time.
  - Two-Phase Locking
    - Growing
      - each txn requests that locks that it needs from the DBMS lock manager
      - lock manager grants/denies lock requests
    - Shrinking 
      - txn is allowed to only releasse/downgrade locks that it previously acquired, cannot acquire new locks.
  - 2PL on its own is sifficient to guarantee conflict serializability because it generates schedules whose precendence graph is acyclic.
  - But it is subject to cascading aborts.
  - Potential schedules that are serializable but would not be allowed by 2PL because locking limits concurrency.
  - May still have "dirty reads".
  - Strong Strict Two-phase locking.
      - Txn is only allowed to release locks after it has ended(committed or aborted)
      - Allows only conflict serializable schedules, but it is often stronger than needed for some apps.
      - a schedule is strict if a value written by a txn is not read or overwritten by other txns unitl that txn finishes.
      - Advantages:
        - does not incur cascading aborts.
        - aborted txns can be undone by just restoring original values of modified tuples.
  - May lead to deadlocks.
    - A deadlock os a cycle of transactions waiting for locks to be released by each other.
      - Deadlock Detection
        - creates a waits-for graph to keep track of what locks each txn is waiting to acquire.
          - nodes are transactions.
          - edge from Ti to Tj if Ti is waiting for Tj to release a lock.
        - system periodically checks for cycles in waits-for graph and then decided how to break it.
      - Deadlock Handling
        - DBMS selects a victim txn to rollback to break the cycle.
        - Either by:
          - By age
          - By progress
          - By the # of items alredy locked
          - By # of txns that we have to rollback with it.
        - Should also consider how many times a txn has been restarted to perevent starvation
        - Rollback length
          - Completely
            - Rollback entire txn and tell the application it was aborted.
          - Partial(Savepoints)
            - DBMS rools back a portion of a txn(to break deadlock) and then attempts to re-execute the undone queries.
        - The victim txn will either restart or abort depending on how it was invoked.
        - Tradeoff between the frequency of checking for deadlocks and how long txns wait before deadlocks are broken.
      - Deadlock Prevention
        - When a txn tries to acquire a lock that is held by another txn, the DBMS kills one of them to prevent a deadlock.
        - This approach does not require a waits-for graph or detection algorithm.
        - Approach
          - Wait-Die.
            - if requesting txn has higher priority than holding txn, then requesting txn waits for holding txn.
            - otherwise requesting txn aborts.
          - Wound-Wait
            - if requesting txn has higher priority than holding txn, then holding txn aborts and releases lock.
            - otherwise requesting txn waits.
        - Why do schemes quarantee no deadlocks
          - only one type of direction allowed when waiting for a lock.
        - When a txn restarts, what is its new priority?
          - its original timestamp to prevent it from getting starved for resources.
      - Acquiring locks is amore expensive operation than acquiring a latch even if that lock is available
  - Lock Granularities
    - DBMS decides the granularity of a lock when a txn wants a lock.
      - Attribute, Tuple, Page, Table
    - DBMS should ideally obtain fewest number of locks that a txn needs.
    - Trade-off parallelism versus overhead
    - Database Lock Hierarchy 
      - Database -> Table -> Page -> Tuple -> Attribute.
  
  - Intention Locks
    - An intention lock allows a higher-level node to be locked in shared or exclusive mode without having to check all descendent nodes
    - If a node is locked in an intention mode, then some txn is doing explicit locking at a lower level in the tree.
    - Types:
      - Intention-Shared
        - intent to get S lcoks at finer granularity.
      - Intention-Exclusive
        - intent to get X lock at finer granularity.
      - Shared+Intention Exclusive
        - subtree rooted by that node is locked explicitly in shared mode and explicit locking is done at a lower level with exclusive-mode locks.
    - Compatibility Matrix
    - Locking Protocol
      - eact txn obtains appropriate lock at the highest level of the database hierarchy.
    - Lock Escalation
      - DBMS can automatically switch to coarse grained locks when a txn acquires too many low-level locks
      - This reduces the number of requests that the lock manager must process.
  - Applications dont acquire a txn's locks manually
  - Need to provide the DBMS with hints to help it improve concurrency.
  - Explicit locks are also useful when doing major changes to the database.
  - Lock Table
  
## Timestamp-Ordering Concurrency Control
  
  - Determine serializability order of txns beofre they execute.
  - Use timestamps to determine the serializability order of txns.
  - Each txn is assigned a unique fixed timestamp that is monotonically increasing.
  - Implementation strategies
    - System/Wall clock
    - Logical counter
    - Hybrid
  - Basic T/O
    - Txns read and write objects without locks
    - Every object X is tagged with timestamp of the last txn that successfully did read/write.
    - Check timestamps for every operation.
    - Thomas Write Rule(Robert H Thomas)
      - ignore the write to allow the txn to continue executing without aborting.
    -  Generates a schedules htat is conflict serializable of you do not use the Thomas write Rules
    - High overhead from copying data to txns workspace and from updating timestamps
      - every read requires the txn to write to the database.
    - Long running txns can get starved
      - likelihood that a txn will read sth from a newer txn increases.
  - If you assume that conflicts between txns are rare and that most txns are short-lived then forcing txns to acquire locks or update timestamps adds unncessary overhead.
  - A better approach is to optimize for the no-conflict case.
  - Optimistic Concurrency Control
    - DBMS creates a provate workspace for each txns
      - any object read is copied into workspace
      - modifications are applied to workspace.
    - When a txn commits, DBMS compares workspace write set to see whether it conflicts with other txns
    - If there are no conflicts, the write set is installed into the global database.
    - Phases:
      - Read Phase
        - track the r/w sets of txns and store their writes in a provate workspace.
        - DBMS copies every tuple that the txn accesses from the shared database to its workspace ensure repeatable reads
      - Validation Phase
        - when a txn commits, check whether it conflicts with other txns.
        - Approaches:
          - Backward Validation - check whether committing txn intersects its read/write sets with those of any txns that have already committed.
          - Forward Validation - check whether the committing txn intersects its read/write sets with any active txns that have not yet committed.
      - Write Phase
        - if validation succeeds, apply private chnages to database, otherwise abort and restart the txn.
        - Serial Commits: use a global latch to limit a single txn to be in the Validation/Write phases at a time.
        - Parallel Commits: use fine-grained write latches to support parallel validation/Write phases
                            txns acquire latches in primary key order to avoid deadlocks.
    - OCC works well when the # of conflicts is low
    - High overhead for copying data locally
    - Validation/Write phase bottlenecks
    - Aborts are more wasteful that in 2PL because they occur after a txn has already executed.
    
  - Dynamic Databases
    - We have only dealt with transactions that read and update existing objects in the database.
    - But now if txns perfomr inserts, updates and deletions, we have new problems.
    - Phantom reads
      - tuples can appear and disappear as txns are running.
      - Approach:
        - Re-execute scans.
          - tracks the WHERE clause for all queries that the tcn executes.
            - retain the scan set for every range query in a txn.
          - upon commit, re-execute just the scan portion of each query and check whether it generates the same result.
        - Predicate Locking
          - HyPer DB.
          - In memory systems.
        - Index Locking Schemes
          - Key-Value Locks
            - locks that cover a single key-value in an index.
            - need virtual keys for non-existant values.
          - Gap Locks
            - each txn acquires a key-value lock on the single key that it wants to access, then get a gap lock on the next key gap.
          - Key-Range Locks
            - locks that cover a ley value and the gap to the next key value in a single index
          - Hierarchical Locking
            - allow for a txn to hold a wider key-range locks with different locking modes.
              - reduces the number of visits to lock manager.
      - Weaker levels of isolation
        - Serializability is useful because it allows programmers to ignore concurrency issues.
        - But enforcing it may allow too little concurrency and limit performance
        - We may want to use a wekaer level of consisitency to improve scalability.
      
      - Isolation Levels
        - Controls the extent that a txn is exposed to the actions of other concurrent txns
        - Provides for greater concurrency at the cost of exposing txns to uncommitted changes
          - Dirty Reads
          - Unrepeatable Reads
          - Phantom Reads
        - Levels:
          - Serializable: No phantoms, all reads repeatbal, no dirty reads.
          - Repeatable Reads: Phantoms may happen.
          - Read Committed: Phantoms and unrepeatable reads may happen.
          - Read Uncommitted: All of them may happen.   
        - How to obtain:
          - Serializable: Obtain all locks first, plus index locks, plus strict 2PL.
          - Repeatable Reads: Same as above but no index locks.
          - Read Committed: Same as abov, but S locks are released immediately.
          - Read Uncommitted: Same as above but allows dirty reads(no S locks).
        
## Multi-Version Concurrency Control
  
  - DBMS maintains multiple physical versions of a single logical object in the database.
    - When a txn writes to an object, the DBMS creates a new verison of that object.
    - When a txn reads an object, it reads the newest version that existed when the txn started.
  - First implemented was db/VMS.
  - Firebird.....influenced firefox browser naming.
  - Key ideas:
    - Writers do not block readers.
    - Readers do not block writers
    - Read-nly txns can read a consistent snapshot without acquiring locks.
      - use timestamps to determine visibility.
    - Easily support time-travel queries
  - Snapshot Isolation
    - when a txn starts, it sees a consistent snaphot of the database that existed when that txn started.
      - No torn writes from active txns
      - If two txns update the same object, then forst writer wins
    - Susceptible to the *Write Skew Anomaly*.
  - MVCC is more than just a concurrency control protocol. It completely affets how the DBMS manages transactions and the database.
  - Concurrency control protocol
    - Timestamp Ordering
    - Optimistic Concurrency Control
    - Two-phase Locking
  - Version Storage
    - DBMS uses the tuples pointer field to create a version chain per logical tuple
      - this allows the DBMS to find the version that is visible to a particular txn at runtime.
      - Indexes always point to the head of the chain.
    - Different storage schemes determine where/what to store for each version
    - Approach:
      - Append-Only Storage
        - new versions are appended to the same table space.
        - on every update, append a new version of the tuple into an empty space in the table.
        - Version chain ordering
          - oldest to newest
          - Newest to Oldest
      - Time-Travel Storage
        - old versions are copied to separate table space.
      - Delta Storage
        - original values of the modified attributes are copied into a separate delta record space.  
        - Overwrite the master version
        - Store the delta values.
  - Garbage Collection
    - DBMS needs to remove reclaimable physical versions from the database over time
      - No active txn in the DBMS can see that version
      - the version was created by an aborted txn
    - Two additional desgin decisions
      - How to look for expired versions
      - How to decide when it is safe to reclaim memory.
    - Approach:
      - Tuple-level
        - Find old versions by examining tuples directly
        - Background Vacuuming.
          - Separate threads periodically scan the table and look for reclaimable versions. Works with any storage.
          - Dirty Block bitMap.
        - Cooperative Cleaning.
          - worker threads ideally scan reclaimable versions as they traverse tuples on operations.
          - only works with Oldest to Newest
      - Transactions level GC
        - Each txn keeps track of its read/write set
        - On commit/abort, the txn provides this information to a centralized vacuum worker.
        - DBMS periodically dtermines when all versions created by a finished txn are no longer visible.
      - Index management 
        - Primary key indexes point to version chain head
          - How often the DBMS must update the pkey index depends on whether the system creates new versions when a tuple is updated.
          - If a txn updates a tuple's pkey attributes then this is treated as a DELETE followed by an INSERT.  
        - Secondary Indexes are more complicated.
        *READ WHY UBER SWITCHED FROM POSTGRES TO MYSQL*
          - Approach:
            - Logical Pointers
              - use a fixed indentifier per tuple that does not change
              - requires an extra indirection layer
              - Primary Key vs Tuple Id
            - Physical Pointers
              - Use the physical address to the version chain head.
        - MVCC Indexes
          - MVCC DBMS indexes usually do not store version information about tuples with their keys
            - exception: Index-organized tables(MySQL)
          - Every index must support deuplicate keys from different snapshots
            - the same key may point to different logical tuples in different snapshots.
      - Transaction-level
        - Txns keep track of their old versions os the DBMS does not have to scan tuples to determine visibility
    

## Database Logging and Shadow Paging

  - Crash Recovery
    - Recovery algorithms are techniques to ensure database consistency, transaction atomiticy and durability despite dailures     
    - Recovery algorithms have two parts
      - Actions during normal txn processing to ensure that the DBMS can recover from a failure.
      - Actions after a failure to recover the database to a state that ensures atomicity, consistency and durability.
  - DBMS is divided into different components based on the underlying storage device
    - Volatile
      - data does not persist after power loss or program exit
      - DRAM, SRAM
    - Non-volatile
      - data persists after power loss and program exit
      - HDD, SDD
    - Stable Storage
      - a non-existent form of non-volatile storage that survives all possible failure scenarios
    - We must also classify the different types of failures that the DBMS needs to handle.
  - Failure Classification
    - Transaction Failures
      - Logical Errors
        - txn cannot complete due to internal error condition(integrity constraint violation)
      - Internal State Errors
        - DBMS must terminate an active transaction due to an error condition(deadlock)
    - System Failures
      - Software Failures
        - problem with the OS or DBMS implementation(divide-by-zero exception)
      - Hardware Failure
        - Computer hosting the DBMS crashes.
        - Fail-stop Assumption: Non-volatile storage contents assumed to not be corrupted by system crash.
    - Storage media Failures
      - Non-repairable Hardware Failure
        - a head crash or similar disk failure destroys all or part of non-volatile storage
        - destruction is assumed to be detectable, (disk controller use checksums to detect failures)
      - No DBMS can recover from this! Database must be restored from archived versions.
  - Observation
    - The DBs primary storage location is on volatile storage but it is slower than volatile storage.Use volatile memory for faster access
      - First copy target record into memory
      - Perform the writes in memory
      - Write dirty records back to disk.
    - The DBMS needs to ensure the following
      - The changes for any txn are durable once the DBMS has said its committed.
      - No partial changes are durable if the txn aborted.
  - Undo vs Redo
    - Undo: Process of removing the effects of an incomplete or aborted txn.
    - Redo: Process of re-applying the effects of a committed txn for durability.
  - How the DBMS supports this functionality depends on how it manages the buffer pool.
    - Steal Policy
      - Whether the DBMS allows an uncommitted txn to overwrite the most recent committed value of an object in non-volatile storage.
    - Force Policy
      - Whether the DBMS requires that all updates made by a txn are reflected on non-volatile storage before the txn can commit.
    - No Steal + Force
      - Easiest approach to implement.
        - Never have to undo changes of an aborted txn because the changes were not written to disk
        - Never have to redo changes of a committed txn because all changes are guaranteed to be written to disk at commit time
      - Cannot support write sets that excedd the amount of physical memory available.
    - Shadow Paging
      - Instead of copying the entire database, the DBMS copies pages on write to create two versions:
        - Master: contains only changes form committed txns
        - Shadow: Temporary db with chnages made form uncommitted txns
      - Buffer Pool Policy: No-steal + Force
      - To install updates when a txn commits, overwrite the root so it points to the shadow, thereby swapping the master and shadow.
      - Supporting rollbacks and recovery is easy.
      - Disadvantages:
        - Copying the entire page tbale is expensive
          - use a page table structured like a B+ TREE
          - No need to copy entire tree, only need to copy path in the tree that lead to updated leaf nodes
        - Commit overhead is high
          - Flush every updated page, page table and root
          - Data gets fragmented(bad for sequential scsns)
          - Need garbage collections
          - Only supports one writer txn at a time or txns in a batch. 
      - SQLITE(PRE-2010)
        - rollback mode.
        - use a journal file
    - We need a way for the DBMS convert random writes into sequential writes
    - Write-Ahead Log
      - maintain a log file separate from data files that contains the changes that txns make to database
        - assume the log is on stable storage
        - log contains enough information to perform the necessary undo and redo actions to restore the database
      - DBMS must write to disk the log file records that correspond to changes made to a db object before it can flush that object to disk
      - Buffer Pool Policy: Steal + No-force
      - WAL Protocol
        - DBMS stages all a txns log records in volatile storage(backed by buffer pool)
        - All log records pertaining to an updated page are written to non-volatile storage before the page itself is over-written in no-volatile storage
        - A txn is not considered committed until all its log records have been written to stable storage.
        - Write a begin record to the log for each txn to mark its starting point.
        - When a txn finishes, DBMS will:
          - Write a commit record on the log
          - Make sure that all log records are flushed before it returns an ack to application.
        - Each log entry contains information about the change to a single object
          - Transaction ID
          - Object ID
          - Before Value(UNDO)
          - After Value(REDO)
      - Flushing the walog buffer to disk every time a txn commits will become a bottleneck
      - DBMS can use the group commit optimization to batch multiple log flushes together to amortize overhead.
      - Almost every DBMS uses No-force+Steal.
      - Logging Schemes
        - Physical Logging
          - Record the byte-level changes made to a specific page
          - e.g git diff
        - Logical Logging
          - Record the high-level operations executed by txns
          - e.g UPDATE, DELETE and INSERT
        - Physiological Logging
          - Hybrid approach with byte-level changes for a single tuple identified by page id+slot number
          - Does not specify organization of the page.
        - Logical logging requires less data written in each log record than physical loggin
        - Difficult to implement recovery with logical loggin if you have concurrent txns running at lower isolation levels
          - Hard to determine whihc parts of the db may have been modified by a query beofre crash.
          - Also takes longer to recover because you must re-execute every txn all over again.
      - Log-structured systems
        - Do not have dirty pages
          - any page retrieved from disk is immutable
        - DBMS buffers log records in in-memory ages(MemTable), If this buffer is full, it must be flushed to disk. But it may contain changes, uncommitted txns
        - DBMS still maintain a separate WAL to recreate the MemTable on crash.
      - Checkpoints
        - WAL will grow forever
        - After a crash, the DBMS must replay the entire log, which will take a long time.
        - DBMS periodically takes a checkpoint where it flushes all buffers out to disk.
          - Provides a hint on how far back it needs to replay the WAL after a crash.
        - Blocking/Consistent Checkpoint Protocol
          - Pause all queries.
          - Flush all WAL records in memory to disk.
          - Flush all modified pages in buffer pool to disk
          - Write a CHECKPOINT entry to WAL and flush to disk.
          - Resume queries.
        - Scanning the log to find uncommitted txns can take a long time.
        - How often the DBMS should take checkpoints depends on many different factors.
          - Frequency
          - Tunable option that depends on application recovery time requirements
          
## Database Recovery
  
  - ARIES: Algorithms for Recovery and Isolation Exploiting Semantics.
  *look and read paper by the same name*
    - Main Ideas
      - Write-Ahead Logging
        - Any chnage is recorded in log on stable storage before the database change is written to disk
        - Must use STEAL+NO-FORCE buffer pool policies.
      - Repeating History during Redo
        - On DBMS restart, retrace actions and restore database to exact state before crash.
      - Logging Changes During Undo
        - Record undo actions to log to ensure actions is not repeated in the event of repeated failures.
    - WAL Records
      - We need to extend our log record format from last class to include additional info
      - Every log record now includes a gloablly unique log sequence number(LSN)
        - LSN represent the physical order that txns make chnages to the database
      - Various components in the system keep track of LSNs that pertain to them.
      - Log Sequence Number
        - flushedLSN - Memory - Last LSN in log on disk
        - pageLSN - page(x) - Newest update to page(x)
        - recLSN - page(x) - Oldest update to page(x) since it was last flushed.
        - lastLSN - T(i) - Latesst record  of txn
        - MasterRecord - Disk - LSN of latest checkpoint.
      - Before the DBMS can write page X to disk, it must flush the log at least to the point where: pageLSN <= flushedLSN
      - Writing Log records
        - All log records have an LSN.
        - Update the pageLSN every time a txn modifies a record in the page.
        - Update the flushedLSN in memory every time the DBMS writes out the WAL buffer to disk.
      - Normal Execution
        - Each txn invokes a sequence of reads and writes, followed by commit or abort.
        - Assumptions
          - All log records fit within a single page.
          - Disk writes are atomic
          - Single-versioned tuples with strong strict 2PL.
          - STEAL + NO-FORCE buffer management with WAL
        - Transaction commit
          - When a txn commits, the DBMS writes a COMMIT record to log and guarantees that all log records up to txns COMMIT record are flushed to disk.
            - Log flushes are sequential, synchronous writes to disk.
            - Many log records per log page.
          - When the commit succeeds, write a special TXN-END record to log.
            - Indicates that no new log record for a txn will appear in the log ever again.
            - This does not need to be flushed immediately.
        - Transaction Abort
          - Abort a txn is a special case of the ARIES undo operation applied to only one txn.
          - We need to add another field to our log records:
            - prevLSN: the previous LSN for the txn.
            - This maintains a linked-list for each txn that mkes it easy to walk through its records.
          - Compensation Log records
            - A CLR describes the actions taken to undo the actions of a previous update record.
            - It has all the fields of an update log record plus the undoNext pointer(the next-to-be-undone LSN).
            - CLRs are added to log records but the DBMS does not wait for them to be flushed before notifying the application that the txn aborted.
          - Abort Algorithm
            - First write an ABORT record to log for the txn.
            - Then analyze the txn updates on reverse order.
            - For each update record:
              - Write a CLR entry to the log.
              - Restore old value.
            - Lastly, write a TXN-END record and release locks.
            - Notice: CLRs never need to be undone.
      - Non-fuzzy checkpoints
        - DBMS halts everything when it takes a checkpoint to ensure a consistent snapshot
          - Halt the start of any new txns
          - Wait until all active txns finish executing
          - Flushes dirty pages on disk.
        - This is bad for runtime performance but makes recovery easy.
      - Btter way to checkpoints
        - pause modifying txns while the DBMS takes the checkpoint.
          - prevent queries from acquiring write latch on table/index pages
          - dont have to wait until all txns finish beofre taking the checkpoint
        - to prevent torn pages
          - we must record internal state as of the beginning of the checkpoint
            - Active Transaction Table(ATT)
              - One entry per currently active txn
                - txnId: Unique txn identifier
                - status: Current mode of the txn.
                - lastLSN: Most recent LSN created by txn
              - Remove entry after the TXN-END record
              - Transaction Status Codes
                - R - Running
                - C - Committing
                - U - Candidate for Undo
            - Dirty Page Table(DPT) 
              - Keep track of which pages in the buffer pool contain changes that have not been flushed to disk.
              - One entry per dirty page in the buffer pool
                - recLSN: The LSN of the log record that first caused the page to be dirty.
      - Fuzzy Checkpoints 
        - A fuzzy checkpoint is where the DBMS allows active txns to continue the run while the system writes the log records for checkpoint
          - No attempt to force dirty pages to disk.
        - New log records to track checkpoint boundaries
          - CHECKPOINT-BEGIN: Indicates the start of checkpoint
          - CHECKPOINT-END:   Contains ATT + DPT
          - Any txn that begins after the checkpoint start is excluded form the ATT in the CHECKPOINT-END record
          - The LSN of the CHECKPOINT-BEGIN record is written to the MasterRecord when it completes.
        - ARIES - Recovery Phases
          - Analysis
            - Examine the WAL in forward direction starting at MasterRecord to identify dirty pages in the buffer pool and active txns at the time of the crash.
            - Figure out which txns committed or failed since last successful checkpoint
              - If DBMS finds a TXN-END record, remove its corresponding txn from ATT.
              - All other records:
                - If txn not in ATT, add it with status UNDO.
                - On commit, change txn status to COMMIT.
              - For update log records
            - At end of Analysis Phase
              - ATT identifies which txns were active at time of crash
              - DPT identifies which dirty pages might not have made it to disk.
          - Redo
            - Repeat all actions starting from an appropriate point in the log(even txns that will abort)
            - Goal is to repeat history to recnstruct the database state at the moment of the crash
              - reapply all updates, even aborted txns and redo CLRs
            - There are techniques that allow the DBMS to avoid unnecessary reads/writes.....*look into this*
            - Scan forward form the log record containing smallest recLSN in DPT.
            - For each update log record or CLR with a given LSN redo the action unless
              - Affected page is not in DPT or
              - Affected page is in DPT but that record's LSN is less than the page's recLSN.
            - To redo an action
              - Reapply logged uodate
              - Set pageLSN to log records LSN.
              - No additional logging, no forced flushes
            - At the end of Redo Phase, write TXN-END log records for all txns with status C and remove them from the ATT.
          - Undo
            - Reverse the actions of txns that did not commit before the crash.
            - Undo all txns that were active at the time of crash and therefore will never commit
              - these are all the txns with U status in the ATT after the Analysis Phase
            - Process them in reverse LSN order using the lastLSN to speed up traversal
            - Write a CLR for every modification.
        - What does the DBMS do if it crashes during recovery in the Analysis Phase?
          - Nothing. Just run recovery again.
        - What does the DBMS do if it crashed during recovery in the Redo Phase?
          - Agian nothing. Redo everything again.
        - How can the DBMS improve performance during recovery in the Redo Phase?
          - Assume that it is not going to crash again and flush all changes to disk asynchronously in the background.
        - How can the DBMS improve performance during recovery in the Undo Phase?
          - Lazily rollback changes before new txns access pages
          - Rewrite the application to avaoid long-runnig txns.
          
## DIstributed Databases

  - Characteristics
    - Nodes can be far from each other.
    - Nodes connected using public netwrok.
  - Use the building blocks that are covered in single node DBMS to now support transaction processing and query execution in distributed environments.
  - A distributed DBMS system arcitecture specifies what shared resources are directly accessible to CPUs
  - This affects how the CPUs coordinate with each other and where they retrieve/store objects in the database.
  - System Architectures
    - Shared Everything
    - Shared Memory
      - CPUs have access to common memory address space via a fast interconnect
        - each processor has a global view of all the in-memory data structures
        - each DBMS instance on a processor must "know" about the other instances
    - Shared Disk*
      - CPUs can access a single logical disk directly via an interconnect, but each have their own private memories
        - can scal execution layer independently form storage layer
        - must send messages between CPUs to learn about their current state.
      - Any OLAP DB in use now.
    - Shared Nothing*
      - Each DBMS instance has its own CPU, memory and local disk
      - Node only communicate with each other via network  
        - Harder to scale capacity
        - Harder to ensure consistency
        - Better performance and efficiency.
  - Design Issues
    - How does the application find data?
    - Where does the application send queries?
    - How to execute queries on distributed data?
      - Push query to data.
      - Pull data to query.
    - How does the DBMS ensure correctness?
    - How do we divide the database across resources?
  - Homogenous Nodes
    - Every node in the cluster can perform the same set of tasks(albeit on potentially different partitions of data)
    - Makes provisioning and failover easier.
  - Heterogenous Nodes
    - Nodes are assigned specific tasks
    - Can allow a single physical node to host multiple "virtual" node types for dedicated tasks.
  - Data Transperancy
    - Apps should not be required to know where data is physically located in a distributed DBMS
      - query on a single node should have same results as distributed db.
  - Database Partitioning
    - Split database across multiple resources
      - Disks, nodes, processors
      - Often called "Sharding"
    - DBMS executes query fragments on each partition and then combines the results to produce a single answer.
    - DBMS can partition physically(shared nothing) or logically(shared disk)
    - Naive TABLE pARTITIONING
      - Assign an entire table to a single node.
      - Assume that each node has enough storage space for an entire table
      - Ideal if queries never join data across tables stored on different nodes and access patterns are uniform.
    - Vertical Partitioning
      - Split a table's attributes into separate partitions.
      - Must store tuple information to reconstruct the original record
    - Horizontal Partitioning
      - Split a table's tuples into disjoint subsets based on some partitioning key and scheme
        - choose column that divided the datase equally in terms of size, load, usage
      - Partitioning schemes
        - Hashing
        - Ranges
        - Predicates
    - Logical Partitioning vs Physical Partitioning
    - Consistent Hashing*
      - Solution to rehashing on mod change.
      - Learn more about this.
    - Single Node vs Distributed
      - A distributed txn accesses data at one or more partitions
        - Requires expensive coordination.
    - Transaction Coordination
      - Need a way to coordinate their execution on the system if our DBMS supports multi-operation and distributed txns.
      - Two different approaches
        - Centralized: Global "traffic cop".
          - TP Monitor: X/Open XA.
          - Middleware: Vitess, planetscale, mongoDB
        - Decentralized: Nodes organize themselves.
          - Leader election type.
    - Distributed Concurrency Control
      - Need to allow multiple txn to execute simultaneously across multiple nodes.
        - many of the same protocols from single node DBMSs can be adapted 
      - This is harder because of
        - Replication
        - Network Communication Overhead
        - Node failures
        - Clock Skew
        
## OLAP VS OLTP
 
  - On-line Transaction Processing(OLTP)
    - Short-lived read/write txns
    - Small footprint
    - Repetitive operations
  
  - On-line Analytical Processing   
    - Long-running read-only queries
    - Complex joins
    - Exploratory queries.

### OLTP
          
  - How to ensure that all nodes agree to commit a txn and then make sure it does commit if we decide that it should.
    - What happends if a node fails?
    - What happens if our messages show up late?
    - What happens if we dont wait for every node to agree?
  
  - Assume all nodes in a distributed DBMS are well-behaved and under the same administrative domain
    - If we tell a node to commit a txn, then it will commit the txn(if there is no failure)
  - If you dont trust the other nodes in a distributed DBMS you need to use a BYzantine Fault Tolerant protocol for txns.
  
  - Atomic Commit Protocol
    - when a multinode txn finishes, the DBMS needs to ask all the nodes involved whether it is safe to commit
    - Examples:
      - Two-Phase commit*
        - Each node records the inbound/outbound messages and outcome of each phase in a non-volatile storage log
        - On recovery examine the log for 2PC messages
          - if txn is in prepared state, contact coord.
          - if txn is not in prepared, abort it.
          - if txn was committing and node is cood, send COMMIT message to nodes.
        - 2PC Optimizations
          - Early Prepare Voting
            - nide return query result if it knows no other query will run there.
          - Early Acknowledge After Prepare
            - if all nodes vote to commit a txn, coord can send client an ack that their txn was successful before the commit phase finishes.
      - Three-Phase commit
      - Paxos*
        - Consensus protocol where a coord proposes an outcome(commit or abort) and then the participants vote on whether that outcome should succeed.
        - Does not block if a majority of participants are available and has provably minimal mesasge delays in the best case.
        - *PAXOS MADE LIVE*
        - K-safety - minimal number of required partitions.
        - Multi-Paxos
          - if the system elects a single leader that oversees proposing changes for some period then it can skip the propose phase.
            - fall back to full Paxos whenever there is a failure
          - The system periodically renews the leader(known as a lease) using another Paxos round
            - Nodes must exchange log entries during leader election to make sure that everyone is up-to-date.
        - 2PC vs Paxos
          - 2PC
            - Blocks if coord fails after the prepapre message is sent, until coodinator recovers.
          - Paxos
            - Non-blocking if a majority participants are alive. provided there is a sufficiently long period without further failures.
      - Raft
      - ZAB(Zookeeper)
      - Viewstamped Replication
  - Replication
    - The DBMS can replicate data across redundant nodes to increase availability.
    - Design decisions:
      - Replica configuration
        - Approach:
          - Primary-Replica
            - All updates go to a designated primary for each object.
            - The primary propagates updates to its replicas without an atomic commit protocol.
            - Read-only txns may be allowed to access replicas.
            - If the primary goes down, then hold an election to select a new primary.
          - Multi-Primary
            - Txns can update data objects at any replica.
            - Replicas must synchronize with each other using an atomic commit protocol.
        - K-Safety
          - Threshold for determining the fault tolerance of the replicated database.
          - The value K represents the number of replicas per data object that must always be available.
          - If the number of replicas goes below thos threshold then DBMS halts execution and takes itself offline.
      - Propagation scheme
        - When a txn commits on a replicated database, the DBMS decides whether it must wait for that txn changes to propagate to other nodes before it can send ack to application
        - Propagation Levels:
          - Synchronous (Strong Consistency)
            - The primary sends updates to replicas and then waits for them to ack that they fully applied(logged) the changes.
          - Asynchronous (Eventual Consistency)
            - The primary immediately returns the ack to the client without waiting for replicas to apply the changes.
            - Mostly used.
      - Propagation Timing
        - Approach
          - Continuous
            - DBMS sends log messages immediately as it generates them.
            - Also need to send a commit/abort message.
          - On Commit
            - DBMS only sends the log messages for a txn to the replicas once the txn is commits
            - Do not waste time sending log records for aborted txns
            - Assumes that a txn log record fits entirely in memory.
      - Update Method
        - Approach
          - Active-Active
            - a txn executes at each replica independently
            - need to check at the end whether the txn ends up with the same result at each replica
          - Active-Passive
            - each txn executes at a single location and propagates the changes to the replica
            - either do physical or logical replication.
            - not the same as primary-replica vs multi-primary
      - Google Spanner
        - Google geo-replicated DBMS
        - Schematized, semi-relational data model
        - Decentralized shared-disk architecture
        - Log-structured on disk storage
        - Cooncurrency Control
          - Strict 2PL + MVCC + Multi-Paxos + 2PC
          - Wound-Wiat Deadlock Prevention.
          - Externally consistent global write-transactions with synchronous replication
          - Lock-free read-only transactions.
          - DBMS ensures ordering through globally unique timestamps generated form atomic clocks and GPS devices.
          - Database is broken up into tablets(partitions)
            - Use Paxos to elect leader in tablet group
            - Use 2PC for txns that span tablets.
        - Transaction Ordering
            - DBMS orders transations based on physical "wall clock" time.
            - Guarantee strict serializability.
            - If T1 finishes before T2 then T2 should see the result of T1.
      - CAP Theorem
        - Consistent, Always Available, Network Partition Tolerant.
        - How a DBMS handles failures determines whoch elements of the CAP theorem they support
          - Traditional/Distributed Relational DBMS
            - stop allowing updates until a majoruty of nodes are reconnected.
          - NoSQL DBMSs
            - provide mechanisms to resolve conflicts after nodes are reconnected.
      - PACELC Theorem

**OLTP --> EXTRACT, TRANSFORM, LOAD --> OLAP**
### OLAP
  
  - Decision Support Systems
    - Applications that serve the management, operations and planning levels of an organization to help people make decisions about future issues and problems by analyzing historical data
  - Star schema vs Snowflake Schema
    - Fat tables
    - Issues
      - Normalization
        - Snowflake schemas take up less storage space.
      - Query Complexity
        - Snowflake schemas require more joins to get the data needed for a query
        - Queries on star schemas will be faster
          
  - Push vs Pull
    - Approach:
      - Push query to data
        - send the query to the node that contains the data.
        - perfomr as mush filtering and processing as possible where data resides before transmitting over network.
      - Pull data to query
        - bring the data to the node that is executing a query that needs it for processing.
  - The data that a node reveives from remote sources are cached in the buffer pool
    - allows DBMS to support interndiate results that are large than the amount of memory availabel
    - ephemeral pages are not persisted after a restart.
  - What happens to a long-running OLAP query if a node crashes during execution??
  - Query Fault Tolerance
    - Most shared nothing distributed OLAP DBMSs are designed to assume that nodes do not fail during query execution
      - if one node fails during query execution, then the whole query fails
    - DBMS could take a snapshot of the intermediate results for a query during execution to allow it to recover of nodes fail.
      - be strategic about this
  - Query Planning
    - All the optimizations fro mabove are still applicable
      - Predicate pushdown
      - Early projections
      - Optimal Join Orderings
    - Distibuted query optimizations is even harder because it must consider the physical location of data and network transfer costs.
    - Query plan fragments
      - Approach:
        - Physical operators
          - generate a single query plan and then break it up into partition specific fragments
          - most systems implement this
        - SQL
          - rewrite the original query into partition-specific queries
          - allows for local optimization at each node.
          - *SingleStore* + *Vitess* use this.
      - The efficiency of a distributed join depends on the target tables partitioning schemes
      - One approach is to put entire tables on a single node and then perform the join
        - You lose the parallelism of a dstributed DBMS
        - Costly data transfer over the network.
        - Does not scale.
  - Distibuted Join Algorithms
    - To join table R and S, the DBMS needs to get the proper tuples on the same node.
    - Once the data is at the node, the DBMS then executes the same join algorithms that we discussed earlier in the semester.
    - Hard to find optimal partition key
    - Semi-Join
      - Join type where the result only contains columns from the left tbale.
      - Distributed DBMSs use semi-join to minimize the amount of data sent during joins
        - This is like a projection pushdown.
      - Some DBMSs support SEMI-JOIN OR fakes it as EXISTS  
  - Cloud Systems
    - DBaaS - manages DBMS environments
    - Newer systems are starting to blur the lines between shared-nothing and shared-disk.
      - You can do sinple filtering on Amazon S3 before copying data to compute nodes.
    - Approach 
      - Manages DBMS
        - No significant modification to the DBMS to be aware that is running in a cloud environment.
      - Cloud-Native DBMS 
        - Explicitly designed to run ina cloud environment
        - Shred-disk architecture.
        - E.G: *Snowflake, Google BigQuery, Amazon Redshift, Ms SQL Azure*
    - Serverless Dbs
      - rather than always maintaining compute resources for each customer a serverless DBMS evicts tenants when they become idle.
      - page in buffer pool and page table or restart.
      - *Neon, Fauna, Planetscale, CockroachDB*
    - Data Lakes
      - Repository for stroing large amounts of structured, semi-structured and unstructured data without having to define a schema or ingest the data into proprietary internal formats
      - *Trino, Redshift, Presto, Databricks, Hive, Snowflake*
  - Universal Formats
    - Most DBMSs use proprietary on-disk binary file format for their databases.
    - The only way to share data between systems is to convert data into a common text-based format
      - CSV, JSON, XML
    - There are new open-source binary file fomrats that make it easier to access data across systems
    - Examples:
      - Apache Parquet
        - compressed columnar storage from cloudera/twitter
        - writeonce read many
      - Apache ORC
        - compressed columnar storage from Apache Hive
      - Apache CarbonData
        - compressed columnar storage with indexes from Huawei
      - Apache Iceberg
        - flexible data format that supports schema evolution from Netflix
      - HDF5
        - multi-dimensional arrays for scientific workloads
      - Apache Arrow 
        - in-memory compressed storage from Pandas/Dremio
  - Disaggreagated Components
    - System catalogs
      - store metadata about the data.
      - HCatalog, Google Data Catalog, Amazon Glue Data Catalog
    - Node management
      - K8S, Apache YARN
    - Query Optimizers
      - Apache Calcite, Greenplum Orca
  
## Embedded Database Logic
  
  - The application has a conversation with the DBMS to store/retrieve data.
    - each DBMS has its own network protocol.
    - client side APIs: JDBC, ODBC.
  - Conversational Database API.
  - Moving application logic into the DBMS can potentially provide sevaral benefits
    - Fewer network round-trips
    - immdiate notification of changes
    - DBMS spends less time waiting during transactions
    - Developers dont have to reimplement functionality
  - User-defined functions
    - A UDF is a functon written by the application developer that extends the system's functionality beyond its built-in operations
      - it takes in input arguments(scalars)
      - perform some computations
      - return a result(scalars, tables)
    - UDF Defn
      - Return Types
        - scalar functions: return single data value
        - table functions: return a single result table.
      - Computation definition
        - sql functions
        - external programming language
      - a SQL-based UDF contains a list of queries that the DBMS executes in order when invoked
        - the function returns the result of the last query executed.
        - SQL standard provides the ATOMIC keyword to tell the DBMS to track dependencies UDFs.
      - also use external programming language
        - sandbox vs non-sandbox
    - Advantages
      - encourage modularity and code reuse
        - different queries can reuse the same application logic without having to reimplement it each time.
      - fewer network round-trips between application server and DBMS for complex operations
      - some types of application logic are easier to express and read as UDFs than SQL.
    - Disavantages
      - Query optimzers treat UDFs as black boxes
        - unable to estimate cost of you dont know that a UDF is going to do when run.
      - It is difficult to parallelize UDFs due to correlated queries inside of them
        - some DBMS will only execute queries with a single thread if they contain a UDF.
        - some UDFs incrementally construct queries.
      - complex UDFs in SLECT/WHERE clauses force the DBMS to execute iteratively
        - RBAR = row by agonizing row
        - things get worse if UDF invokes queries doe to implicit joins that the optimzer coannot see.
      - since the DBMS executes the commands in the UDF one-by-one, its unable to perform cross-statement optimizations.
  - Stored Procedures
    - A stored procedure is a self-contained function that perfroms more complex logic inside of the DBMS
      - cna have many input/output parameters
      - can modify the database table/structures
      - not normally used within a SQL query
    - Some DBMS distinguish UDFs vs stored procedures, but not all.
      - A UDF is meant to perform a subset of a read-only computations within a query.
      - A stored procedure is meant to perform a complete computations that is independent of a query.
  - Database Triggers
    - A trigger instructs the DBMS to invoke a UDF when some event occurs in the database.
    - The developer has to define
      - What type of event will cause it to fire
      - The scope of the event
      - When it fires relative to that event.
    - Event Type: INSERT, UPDATE, DELETE, TRUNCATE, CREATE, ALTER, DROP
    - Event Scope: TABLE, DATABASE, VIEW, SYSTEM
    - Timing: Before, After query, row
  - Change Notifications
    - A chnage notification is like a trigger except that the DBMS sends a message to an external entity that something notable has happened to the db.
      - think pub/sub system
      - can be chained with a trigger to pass along whenever a change occurs
      
    - SQL STANDARD: LISTEN + NOFITY
  - All DBMSs support the basic primitive types in the SQL standard, basic arithmetic and string manipulation on them.
  - if we want to store data that doesnt match any of the built-in types?
  - Complex Types
    - Attribute splitting
    - Application serialization
  - User-defined Types
    - is a special data type that is defined by the application developer that the DBMS can store natively
      - introduced by Postgres in 1980
      - added to SQL:1999 
    - also referred to as structured user-defined types or structured types.
    - each DBMS exposes apis to create UDT.
  - Views
    - Creates a virtual table containing the putput from a SELECT query. The view can then be accessed as if it was a real table.
    - This allows programmers to simplify a complex query that is executed often
      - wont make it run faster
    - Often used a mechanism for hiding a subset of table's attributes from certain users.
  - Select...into 
    - creates static table that does no get updated when student gets updated.
  - Materialized Views
    - creates a view containing the output from a SELECT query that is reatined(not recomputed)
      - some DBMSs automatically update matviews when the underlying tables chage
      - other DBMSs(postgresql) require manual refresh.
  - Disavantages:
    - Not portable
    - DBSs dont like change
    - Potentially need tomaintain different versions.
    