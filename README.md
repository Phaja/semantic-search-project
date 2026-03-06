Semantic Search System with Cluster-Aware Caching
Project Overview

This repository contains a high-performance semantic search engine designed to handle large-scale text datasets. The system leverages dense vector embeddings and a custom-built semantic caching layer to optimize retrieval speed and computational efficiency.

Unlike traditional keyword-based search systems, this engine understands the intent and contextual meaning of user queries, enabling more relevant search results.

Technical Architecture

The system is built using a multi-stage retrieval pipeline designed for high efficiency and scalability.

1. Vectorization Layer

The system utilizes the Sentence-Transformers all-MiniLM-L6-v2 model to convert raw text into 384-dimensional dense vector embeddings.

Why this model?

High-quality semantic embeddings

Lightweight and CPU-efficient

Fast inference suitable for real-time applications

These embeddings allow the system to perform semantic similarity comparisons instead of simple keyword matching.

2. Gaussian Mixture Model (GMM) Clustering

To avoid expensive full-vector scans, the corpus is partitioned using a Gaussian Mixture Model (GMM).

Routing

Each incoming query is assigned a Dominant Cluster ID based on the highest probability score produced by the GMM.

Efficiency

By routing queries to the most relevant cluster:

Search operations are performed only on a subset of vectors

Reduces overall search complexity

Improves retrieval speed

3. FAISS Indexing

For efficient nearest neighbor search, the system uses FAISS (Facebook AI Similarity Search).

FAISS provides:

Highly optimized C++ implementations

Fast vector similarity computations

Sub-millisecond search performance

Even when the cache is missed, the FAISS index ensures very fast fallback retrieval.

4. Cluster-Aware Semantic Cache

The core innovation of this project is the Cluster-Aware Semantic Cache.

Mechanism

Before querying the FAISS index, the system checks a localized semantic cache associated with the query’s dominant cluster.

Similarity Threshold

If a previous query in the same cluster has a Cosine Similarity ≥ 0.85, the system immediately returns the cached result.

Example

Queries like:

"how to fix a car"

"automobile repair guide"

may map to the same semantic representation and trigger a cache hit.

Benefits

Avoids redundant neural embedding computations

Reduces FAISS index queries

Improves response latency

Optimizes CPU utilization

System Flow

User submits a query through the FastAPI endpoint

The query text is cleaned and normalized

The text is converted into a vector embedding

The GMM model assigns a dominant cluster

The Semantic Cache is checked within that cluster

If a high-similarity cached query exists → cached result returned

If no match exists → FAISS index is queried

The result is stored in cache for future queries

Response is returned with metadata including:

Cluster ID

Cache status (Hit/Miss)

Performance Optimization
Asynchronous Processing

The system uses FastAPI’s asynchronous request handling, allowing it to process multiple queries concurrently without blocking the event loop during ML computations.

Memory Management

All vector stores and indices are maintained in-memory, enabling:

Zero-latency retrieval

Faster similarity comparisons

Optimized RAM usage through efficient vector structures

Key Features

Semantic understanding of queries

Cluster-based search optimization

High-speed FAISS vector retrieval

Cluster-aware semantic caching

Sub-millisecond search performance

Asynchronous API architecture
