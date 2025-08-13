# BGE Embedding Testing Guide

## 🎯 Overview
BGE (BGE-Embedding) converts text into high-dimensional vectors for semantic similarity and retrieval. This guide covers comprehensive testing strategies.

---

## 🧠 Core Concepts

### What are BGE Embeddings?
- **Purpose**: Text-to-vector conversion for semantic understanding
- **Dimensions**: 768-1024 dimensional vectors
- **Applications**: Search, clustering, similarity matching
- **Languages**: Multi-language support

### Key Testing Areas
1. **Semantic Quality**: Meaning preservation
2. **Performance**: Speed and efficiency
3. **Robustness**: Edge cases and errors
4. **Consistency**: Reproducible results

---

## 🧪 Testing Framework

### 1. Semantic Similarity Testing
```python
def test_semantic_similarity():
    # Test cases
    similar_pairs = [
        ("cat", "feline"),
        ("python", "programming language"),
        ("hello", "hi")
    ]
    
    for text1, text2 in similar_pairs:
        emb1 = bge_model.encode(text1)
        emb2 = bge_model.encode(text2)
        similarity = cosine_similarity(emb1, emb2)
        assert similarity > 0.7, f"Low similarity: {similarity}"
```

### 2. Performance Testing
```python
def test_performance():
    # Latency test
    start_time = time.time()
    embedding = bge_model.encode("test text")
    latency = (time.time() - start_time) * 1000
    
    assert latency < 100, f"High latency: {latency}ms"
    
    # Throughput test
    batch_size = 100
    texts = ["test"] * batch_size
    start_time = time.time()
    embeddings = bge_model.encode(texts)
    throughput = batch_size / (time.time() - start_time)
    
    assert throughput > 10, f"Low throughput: {throughput} texts/sec"
```

### 3. Cross-lingual Testing
```python
def test_cross_lingual():
    multilingual_pairs = [
        ("Hello", "Hola"),
        ("Good morning", "Buenos días"),
        ("Thank you", "Gracias")
    ]
    
    for eng, spa in multilingual_pairs:
        eng_emb = bge_model.encode(eng)
        spa_emb = bge_model.encode(spa)
        similarity = cosine_similarity(eng_emb, spa_emb)
        assert similarity > 0.6, f"Cross-lingual similarity low: {similarity}"
```

### 4. Robustness Testing
```python
def test_robustness():
    edge_cases = [
        "",  # Empty string
        "a" * 1000,  # Very long text
        "🚀🌟💻",  # Emojis
        "1234567890",  # Numbers
        "   whitespace   "  # Whitespace
    ]
    
    for text in edge_cases:
        try:
            embedding = bge_model.encode(text)
            assert embedding is not None
        except Exception as e:
            print(f"Error with edge case '{text}': {e}")
```

---

## 📊 Evaluation Metrics

### Quality Metrics
- **Semantic Similarity Score**: 0.8+ for similar texts
- **Cross-lingual Score**: 0.6+ for translations
- **Clustering Quality**: Silhouette score > 0.3

### Performance Metrics
- **Latency**: < 100ms for single text
- **Throughput**: > 10 texts/sec
- **Memory Usage**: < 1GB for 1000 texts

### Consistency Metrics
- **Reproducibility**: > 0.999 similarity for same input
- **Vector Norm**: 0.9-1.1 range
- **No NaN/Inf**: Clean numerical values

---

## 🔧 Implementation Testing

### Model Loading
```python
def test_model_loading():
    model = BGEEmbeddingModel()
    assert model.model_name is not None
    assert model.embedding_dim > 0
    
    # Test inference
    embedding = model.encode("test")
    assert embedding is not None
```

### Batch Processing
```python
def test_batch_consistency():
    texts = ["Text 1", "Text 2", "Text 3"]
    
    # Individual vs batch
    individual = [bge_model.encode(t) for t in texts]
    batch = bge_model.encode(texts)
    
    for i, (ind, bat) in enumerate(zip(individual, batch)):
        similarity = cosine_similarity(ind, bat)
        assert similarity > 0.999, f"Inconsistency at index {i}"
```

### Error Handling
```python
def test_error_handling():
    invalid_inputs = [None, 123, ["text", 123]]
    
    for invalid in invalid_inputs:
        try:
            bge_model.encode(invalid)
            assert False, f"Should raise error for {invalid}"
        except (ValueError, TypeError):
            pass  # Expected
```

---

## 🚀 Production Testing

### API Testing
```python
def test_embedding_api():
    response = requests.post("/embeddings", 
                           json={"text": "test"})
    
    assert response.status_code == 200
    result = response.json()
    assert "embedding" in result
    assert len(result["embedding"]) > 0
```

### Load Testing
```python
async def load_test():
    async with aiohttp.ClientSession() as session:
        tasks = []
        for i in range(100):
            payload = {"text": f"test {i}"}
            task = session.post("/embeddings", json=payload)
            tasks.append(task)
        
        responses = await asyncio.gather(*tasks)
        success_rate = sum(1 for r in responses if r.status == 200) / len(responses)
        assert success_rate > 0.95
```

### Monitoring
```python
def test_monitoring():
    metrics = {
        "latency_p95": 150,
        "throughput": 1000,
        "error_rate": 0.01,
        "memory_usage": 2048
    }
    
    thresholds = {
        "latency_p95": 200,
        "throughput": 500,
        "error_rate": 0.05,
        "memory_usage": 4096
    }
    
    for metric, value in metrics.items():
        assert value <= thresholds[metric], f"{metric} threshold exceeded"
```

---

## 📈 Continuous Improvement

### A/B Testing
```python
def test_model_versions():
    versions = ["bge-v1.5", "bge-v2.0", "bge-v2.1"]
    results = {}
    
    for version in versions:
        model = load_model(version)
        accuracy = evaluate_benchmark(model)
        latency = measure_latency(model)
        results[version] = {"accuracy": accuracy, "latency": latency}
    
    best_model = max(results.keys(), 
                    key=lambda v: results[v]["accuracy"] / results[v]["latency"])
    return best_model
```

### Drift Detection
```python
def detect_drift():
    baseline = {"semantic_score": 0.85, "clustering_score": 0.75}
    current = calculate_current_metrics()
    
    drift_threshold = 0.1
    for metric, baseline_val in baseline.items():
        drift = abs(current[metric] - baseline_val)
        if drift > drift_threshold:
            trigger_model_update()
            return True
    return False
```

---

## 🎓 Learning Path

### 1. Foundation (1 month)
- Learn Python and NumPy
- Understand vector operations
- Study cosine similarity and distance metrics

### 2. BGE Specific (1 month)
- Install and configure BGE
- Practice with simple examples
- Understand embedding properties

### 3. Testing (1 month)
- Implement test cases
- Set up CI/CD pipeline
- Practice with real datasets

### 4. Production (Ongoing)
- Monitor performance
- Handle edge cases
- Optimize for scale

---

## 📚 Resources

### Documentation
- Hugging Face BGE docs
- Sentence Transformers library
- BAAI BGE GitHub

### Papers
- "Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks"
- "BGE: BAAI General Embedding"

### Tools
- Weights & Biases for experiment tracking
- MLflow for model management
- TensorBoard for visualization

---

**Key Takeaway**: BGE testing requires focus on semantic quality, performance, and robustness. Start with basic similarity tests, then expand to comprehensive evaluation frameworks.
