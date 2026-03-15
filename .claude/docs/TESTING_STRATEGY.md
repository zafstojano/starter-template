# Testing Strategy

Testing philosophy and patterns for Python projects, with emphasis on ML/data workflows.

## Testing Hierarchy

Tests are organized by scope and signal:

### 1. Unit Tests (Fastest, Most Isolated)

Test individual functions and classes in isolation.

**Good candidates:**
- Pure functions (data transformations, calculations)
- Pydantic/dataclass validation
- Utility functions

**Example:**
```python
def test_normalize_handles_empty_array():
    with pytest.raises(ValueError):
        normalize(np.array([]))
```

### 2. Integration Tests (Medium Scope)

Test component interactions.

**Good candidates:**
- Data pipeline stages
- API client interactions
- File I/O workflows

## ML/Data Testing Patterns

### Tensor/Array Shape Assertions

Always verify shapes explicitly:

```python
def test_model_output_shape():
    model = MyModel()
    x = torch.randn(32, 10)
    output = model(x)
    assert output.shape == (32, 5), f"Expected (32, 5), got {output.shape}"
```

### Dry-Run Mode for Training

Skip actual training in tests:

```python
def test_training_loop_runs(tmp_path):
    config = TrainConfig(
        epochs=1,
        batch_size=2,
        dry_run=True,  # Skip GPU, use tiny data
    )
    result = train(config, output_dir=tmp_path)
    assert result.completed
```

### Deterministic Seeding

Ensure reproducibility:

```python
@pytest.fixture
def deterministic():
    torch.manual_seed(42)
    np.random.seed(42)
    random.seed(42)
    yield
```

### Mocking External Services

Use `moto` for AWS, fixtures for file I/O:

```python
@pytest.fixture
def mock_s3():
    with moto.mock_s3():
        client = boto3.client("s3")
        client.create_bucket(Bucket="test-bucket")
        yield client

def test_upload_to_s3(mock_s3):
    upload_file("data.csv", "test-bucket")
    # Verify upload happened
```

### Testing Data Transformations

Use small, handcrafted examples:

```python
def test_normalize_preserves_relative_order():
    data = np.array([1.0, 2.0, 3.0])
    result = normalize(data)
    assert result[0] < result[1] < result[2]
```

## Edge Cases to Consider

### Data-Specific
- Empty datasets
- Single-row datasets
- Missing values (NaN, None, empty strings)
- Type coercion (int -> float)
- Shape mismatches

### Numerical
- Division by zero
- Overflow/underflow
- Floating point precision
- Negative values where positive expected

### Input Handling
- Empty input
- Malformed input (wrong columns, bad encoding)
- Very large input (memory limits)

## Anti-Patterns to Avoid

### Over-Mocking
Don't mock things that are fast and deterministic:
```python
# BAD: Mocking numpy
with patch("numpy.mean") as mock_mean:
    mock_mean.return_value = 5.0
    result = compute_stats(data)

# GOOD: Just use real numpy
result = compute_stats(data)
assert result["mean"] == pytest.approx(5.0)
```

### Flaky Tests
Avoid tests that depend on:
- Network availability
- Timing/sleep
- Random values without seeding
- Filesystem state from other tests

### Testing Implementation Details
Test behavior, not internals:
```python
# BAD: Testing internal state
assert model._hidden_layer.weight.shape == (64, 32)

# GOOD: Testing observable behavior
assert model(input).shape == expected_shape
```

### Redundant Tests
If test A failing would always cause test B to fail, you probably only need test B.

## Running Tests

```bash
# Full suite
uv run pytest tests/ -v --tb=short

# Single file
uv run pytest tests/test_module.py -v

# Single test
uv run pytest tests/test_module.py::test_name -v

# With coverage
uv run pytest tests/ --cov=src/
```

## TDD Workflow Integration

1. **Red**: `/write-tests` creates failing tests
2. **Green**: `/implement` makes tests pass
3. **Refactor**: `/simplify` cleans up code
4. **Validate**: `/pre-submit-pr` runs full suite
