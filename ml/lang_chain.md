# Lang Chain and Vector Databases

`LLMs and private data go super nova`
`A vector database indexes and stores vector embeddings for fast retrieval and similar search with capabilities like CRUD operations, metadata filtering
and horizontal scaling`
``

## Introduction

- Foundation models
- Framework for developing applications powered by language models.
- Not only interact via an api but apps also need to be data-aware(connect llm to other sources of data) and be agentic(llm to interact with its environment).
- 

## Components

- Prompts
- LLMs
- Indexes
- Memory
- Chains
- Agents


## Use cases

- Personal Assistants
- Question Answering over Docs
  - Ingestion
    - Load documents(document loader)
    - Split documents(text splitter)
    - Create Embeddings for documents(text embedding model)
    - Store documents and embeddings in a Vs.
  - Retrieval Augmented Generation Chain
    - Receive user question
    - Lookup documents in the index relevant to the question
    - Construct a promptvalue from the question and any relevant questions 
    - Pass the promptvalue to a model
    - Get back result and return to the user.
- Chatbots
- Querying Tabular data
  - Document loading
  - Querying 
  - 
- Interacting with APIs
  - Chains
  - Agents
- Extraction
  - Output Parsers
- Evaluation
- Summarization

## Vector databases

- Index vector embeddings
  - VE represent inputs like text as numbers, low dimensional representation of high-dimensional data.
- Excel at similarity search
  - Semantic similarity of these objects and concepts can be quantified by how close they are to each other as points in vector space.
- VS enables users to describe what they want to find without having to know which keywords or metadata classifications are ascribed to stored objects.
- Also return results that are similar or near neighbor matches
- VS compares similarity of multiple objects to a search queryor subject item.
- In order to find similar matches you convert the subject item or queries into a vector using the same ML embedding model used to create vector embeddings.
- Common use cases:
  - Semantic search.
  - Similarity search for unstructured data
  - Ranking and recommendation engines
  - Deduplicating and record matching
  - Anomaly detection.

- Required capabilites of Vector Databases
  - Vector indexes for search and retrieval
  - Single-stage filtering
  - Data sharding
  - Replication
  - Hybrid storage
  - API
  
