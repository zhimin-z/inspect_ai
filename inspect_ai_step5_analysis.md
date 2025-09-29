# Inspect AI - Step 5 Support Analysis

## Summary Table
| Process | Support Level | Evidence |
|---------|--------------|----------|
| S5P1 | 3 | Comprehensive statistical metrics, score reducers, DataFrame extraction, bootstrap support |
| S5P2 | 2 | DataFrame slicing via columns, sample metadata filtering, grouped metrics, but no dedicated stratification tools |
| S5P3 | 1 | Log viewer for exploration, external Inspect Viz recommended, no built-in plotting |
| S5P4 | 1 | DataFrame analysis possible with custom code, no built-in tradeoff analysis tools |
| S5P5 | 2 | Frontier analysis, model comparison via DataFrames, statistical significance testing available |

## Detailed Analysis

### S5P1: Compute aggregate statistics
**Rating:** 3 (Good Support)
**Evidence:**
- Built-in metrics in `src/inspect_ai/scorer/_metrics/`: `accuracy()`, `mean()`, `std()`, `stderr()`, `var()`
- Score reducers in `src/inspect_ai/scorer/_reducer/`: `mean_score()`, `median_score()`, `mode_score()`, `max_score()`
- Advanced statistics: `bootstrap_stderr()` for bootstrap standard errors, `stderr(cluster=key)` for clustered standard errors
- `grouped()` metric for stratified statistics by metadata keys
- DataFrame extraction via `evals_df()`, `samples_df()` enables custom aggregation with pandas
- `score_to_float()` operation for numeric conversion of complex score objects
- Classification metrics: F1 score via `f1()` scorer, exact match via `exact()` scorer
- Statistical significance testing possible through bootstrap and standard error calculations
**Limitations:**
- No built-in percentile/quantile calculations beyond median
- Limited weighted aggregation support
- No dedicated outlier detection utilities

### S5P2: Perform stratified analysis
**Rating:** 2 (Partial Support)
**Evidence:**
- Sample metadata expansion via `SampleColumn("metadata_*", path="metadata")` in DataFrame extraction
- DataFrame filtering capabilities through `list_eval_logs()` with custom filter functions
- Column-based slicing with pre-built column groups: `EvalInfo`, `EvalModel`, `SampleSummary`
- `grouped()` metric enables stratified statistics by any metadata key
- Custom column definitions for flexible data extraction
- Multi-dimensional filtering possible via pandas DataFrame operations post-extraction
- Configuration shows support for complex metadata structures
**Limitations:**
- No dedicated stratification tools or APIs
- No built-in fairness metrics or bias analysis tools
- Limited cross-tabulation utilities
- Requires significant custom code for advanced stratified analysis

### S5P3: Generate diagnostic visualizations
**Rating:** 1 (Basic Support)
**Evidence:**
- `inspect view` log viewer provides interactive exploration of evaluation results
- DataFrame extraction (`evals_df`, `samples_df`, `messages_df`, `events_df`) enables external plotting libraries
- `log_viewer()` operation for mapping log file paths to published URLs
- External [Inspect Viz](https://meridianlabs-ai.github.io/inspect_viz/) package specifically recommended for visualization
- Web-based log viewer includes sample-level inspection and trajectory analysis
**Limitations:**
- No built-in plotting capabilities or visualization APIs
- No confusion matrix, ROC curve, or calibration plot generation
- No diagnostic chart generation tools
- Relies entirely on external visualization libraries and tools

### S5P4: Analyze performance-quality tradeoffs and failure patterns
**Rating:** 1 (Basic Support)
**Evidence:**
- Access to timing data via `EventTiming` columns in `events_df()`
- Model usage statistics available in `EvalStats` and sample-level `model_usage` fields
- Error analysis possible via `error` field in sample data
- DataFrame structure enables custom tradeoff analysis through pandas operations
- Sample-level metadata supports custom failure pattern analysis
**Limitations:**
- No built-in tradeoff analysis tools or curves
- No systematic failure pattern detection algorithms
- No performance profiling utilities
- No bias detection capabilities
- Requires extensive custom implementation for meaningful analysis

### S5P5: Rank and compare models against baselines
**Rating:** 2 (Partial Support)
**Evidence:**
- `frontier()` operation in `src/inspect_ai/analysis/_prepare/frontier.py` identifies top-scoring models over time
- Model comparison via DataFrames with `EvalModel` and `EvalScores` column groups
- `model_info()` operation adds model metadata (organization, display name, release date) for comparison
- Statistical significance testing available via `stderr()` and `bootstrap_stderr()` metrics
- Baseline tracking through DataFrame filtering and comparison operations
- Support for multiple evaluation metrics enables comprehensive model ranking
**Limitations:**
- No dedicated leaderboard generation tools
- Limited automated ranking functions
- No built-in statistical significance testing between models
- Requires custom code for comprehensive model comparison workflows

## Configuration Examples

### Statistical Aggregation
```python
from inspect_ai.scorer import accuracy, mean, stderr, grouped
from inspect_ai.analysis import evals_df, samples_df

# Built-in metrics with grouping
@scorer(metrics=[
    accuracy(), 
    mean(), 
    stderr(),
    grouped(accuracy(), "difficulty_level")
])
def my_scorer(): ...

# DataFrame-based analysis
df = samples_df("logs")
# Custom aggregation via pandas operations
```

### Stratified Analysis
```python
from inspect_ai.analysis import samples_df, SampleSummary

# Extract samples with metadata expansion
df = samples_df("logs", columns=SampleSummary)
# Metadata fields are automatically expanded into separate columns
# Custom stratification via pandas groupby operations
```

### Model Comparison
```python
from inspect_ai.analysis import evals_df, prepare, model_info, frontier

df = evals_df("logs")
df = prepare(df, [
    model_info(),    # Add model metadata
    frontier()       # Identify frontier models
])
# Custom ranking and comparison logic
```

## Overall Assessment

Inspect AI provides **good support for basic statistical aggregation (S5P1)** with comprehensive built-in metrics, score reducers, and bootstrap capabilities. **Stratified analysis (S5P2) and model comparison (S5P5)** have **partial support** through DataFrame extraction and some specialized functions, but require custom implementation for advanced use cases.

**Diagnostic visualization (S5P3) and performance-quality tradeoff analysis (S5P4)** have **basic support** primarily through data access, with visualization and advanced analysis requiring external tools and custom code.

The framework excels at providing structured access to evaluation data and basic statistical operations, making it well-suited for users comfortable with pandas and custom Python development rather than providing out-of-the-box advanced analysis solutions.