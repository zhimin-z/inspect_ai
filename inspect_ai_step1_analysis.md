# Inspect AI - Step 1 Support Analysis

## Summary Table
| Process | Support Level | Evidence |
|---------|--------------|----------|
| S1P1 | 3 | Full task registry, flexible task definitions, comprehensive scorer/metric system |
| S1P2 | 3 | Built-in objective/subjective scorers, batch/streaming evaluation, multiple paradigms |
| S1P3 | 3 | Built-in datasets, HuggingFace integration, custom dataset APIs, example datasets |
| S1P4 | 3 | 20+ model providers, comprehensive sandbox system, resource management |
| S1P5 | 3 | Multiple evaluator types, LLM-as-judge, custom evaluators, human-in-the-loop |
| S1P6 | 3 | Rate limiting, cost tracking, timeout configs, approval system, sandboxing |

## Detailed Analysis

### S1P1: Define evaluation tasks and success criteria
**Rating:** 3 (Good Support)
**Evidence:**
- **Task Registry System**: Framework provides a comprehensive `@task` decorator and registry system (`src/inspect_ai/_eval/registry.py`) allowing custom task definitions with flexible parameters
- **Success Criteria Configuration**: Built-in scorer system (`src/inspect_ai/scorer/`) supports multiple evaluation criteria:
  - Correctness: `exact()`, `match()`, `includes()` scorers
  - Safety: Custom scorers can implement safety checks
  - Performance: Built-in metrics with `accuracy()`, `stderr()`, custom metrics support
  - Retrieval Quality: `f1()` scorer for retrieval evaluation
- **Configurable Task Definitions**: Tasks defined through code with extensive configuration options via `.env` files, YAML/JSON configs, and CLI parameters
- **Example Tasks**: Multiple example tasks showing different evaluation objectives (`examples/security_guide.py`, `examples/biology_qa.py`, `examples/theory_of_mind.py`)
- **Metric System**: Comprehensive metrics system with built-in reducers (`mean`, `median`, `mode`, `max`) and custom metric support

**Limitations:**
- Requires Python coding for task definition (no pure configuration-based approach)

### S1P2: Select evaluation methodologies  
**Rating:** 3 (Good Support)
**Evidence:**
- **Objective Metrics**: Built-in scorers for exact matching, pattern matching, F1 scores, and text similarity
- **Subjective Assessment**: Model-graded evaluation with `model_graded_qa()` and `model_graded_fact()` scorers
- **Real-time vs Batch**: 
  - Real-time evaluation through standard API calls
  - Batch evaluation support via `--batch` parameter with configurable batch sizes
  - Streaming support through async architecture
- **Evaluation Paradigms**:
  - Classification: `choice()` scorer with `multiple_choice()` solver
  - Generation: `generate()` solver with various text scorers
  - Ranking: Custom scorers can implement ranking evaluation
  - Dialog: Multi-turn conversation support through solver chaining
- **Evaluation Modes**: Configuration via CLI options (`docs/options.qmd`) including `--epochs`, `--sample-shuffle`, `--limit`

**Limitations:**
None significant - comprehensive coverage of evaluation methodologies

### S1P3: Choose appropriate datasets and benchmarks
**Rating:** 3 (Good Support)
**Evidence:**
- **Standard Benchmarks**: While core framework doesn't include MMLU/HumanEval directly, it references the separate [Inspect Evals](https://ukgovernmentbeis.github.io/inspect_evals/) repository with community-contributed benchmarks
- **Built-in Example Datasets**: 5 example datasets included (`src/inspect_ai/dataset/_examples/`): security_guide, theory_of_mind, popularity, biology_qa, bias_detection
- **Custom Dataset Support**: 
  - Multiple format support: CSV, JSON, JSONL (`src/inspect_ai/dataset/_sources/`)
  - HuggingFace integration (`src/inspect_ai/dataset/_sources/hf.py`)
  - Custom dataset loaders with `FieldSpec` for field mapping
  - `RecordToSample` functions for complex data transformations
- **Dataset API**: Flexible `Dataset` interface supporting memory datasets, file-based datasets, and custom readers
- **Data Processing**: Support for multimodal data (images, audio, video), file attachments, and setup scripts

**Limitations:**
- Popular benchmarks require separate package installation

### S1P4: Configure evaluation infrastructure
**Rating:** 3 (Good Support)
**Evidence:**
- **Model API Support**: 20+ model providers supported (`docs/_model-providers.md`):
  - Lab APIs: OpenAI, Anthropic, Google, Grok, Mistral, DeepSeek, Perplexity
  - Cloud APIs: AWS Bedrock, Azure AI
  - Open (Hosted): Groq, Together AI, Fireworks AI, Cloudflare, SambaNova, Goodfire
  - Open (Local): HuggingFace, vLLM, Ollama, llama-cpp-python, SGLang, TransformerLens
- **Resource Management**: 
  - `--max-connections` for API parallelism control
  - `--max-samples`, `--max-tasks`, `--max-subprocesses` for compute allocation
  - `--timeout`, `--max-retries` for reliability
- **Sandboxing/Isolation**: Comprehensive sandboxing system (`docs/sandboxing.qmd`):
  - Docker containers (built-in)
  - Kubernetes support (extension)
  - EC2 VMs (extension)  
  - Local sandbox for controlled environments
- **Configuration Management**: Environment variables, `.env` files, YAML/JSON configs, and CLI options

**Limitations:**
None significant - excellent infrastructure configuration support

### S1P5: Design evaluator pipeline
**Rating:** 3 (Good Support)
**Evidence:**
- **Rule-based Evaluators**: Built-in text matching scorers (`includes()`, `match()`, `exact()`, `pattern()`)
- **LLM-as-judge**: 
  - `model_graded_qa()` for open-ended answer assessment
  - `model_graded_fact()` for factual accuracy checking
  - Custom model-based scorers with dedicated grader models
- **Human-in-the-loop**: 
  - Human approval system (`docs/approval.qmd`) with fine-grained policies
  - `human_agent()` solver for human baseline evaluation
  - Interactive approval for tool calls and decisions
- **Custom Evaluator APIs**: 
  - `@scorer` decorator for custom scorers
  - Flexible scorer interface with state access
  - Chaining multiple evaluators in pipeline
- **Solver Pipeline**: Extensive solver system (`docs/solvers.qmd`) with chains, prompt engineering, critique, and multi-turn dialog

**Limitations:**
None significant - comprehensive evaluator pipeline support

### S1P6: Set up security and resource constraints
**Rating:** 3 (Good Support)
**Evidence:**
- **API Rate Limiting**: 
  - `--max-connections` parameter for concurrent request control
  - Provider-specific rate limiting configuration
  - Exponential backoff and retry mechanisms
- **Budget Constraints**: 
  - Cost tracking through detailed logging
  - `--limit` and `--sample-id` for controlled evaluation scope
  - `--epochs` control for cost management
- **Timeout Configuration**: 
  - `--timeout` for API request timeouts
  - `--time-limit` for per-sample time constraints
  - `--working-limit` for active processing time limits
- **Security Features**:
  - Credential management through environment variables and `.env` files
  - Sandboxing system isolates untrusted code execution
  - Tool approval system (`docs/approval.qmd`) for security-sensitive operations
  - Container-based isolation with Docker/Kubernetes support
- **Resource Limits**: 
  - `--message-limit`, `--token-limit` for usage constraints
  - `--max-tool-output` for tool output size limits
  - `--fail-on-error` thresholds for error tolerance

**Limitations:**
None significant - excellent security and resource constraint support

## Overall Assessment

Inspect AI demonstrates **excellent support (3/3)** for all Step 1 (CONFIGURE) processes in the evaluation workflow. The framework provides:

- **Comprehensive Configuration**: Extensive options via CLI, environment variables, and config files
- **Flexible Architecture**: Plugin-based system supporting custom datasets, scorers, solvers, and model providers  
- **Production-Ready Features**: Robust error handling, retry mechanisms, logging, and monitoring
- **Security-First Design**: Built-in sandboxing, approval systems, and resource constraints
- **Developer-Friendly**: Well-documented APIs, extensive examples, and VS Code integration

The framework excels in providing both ease-of-use for standard evaluations and extensibility for complex custom evaluation scenarios. Its modular design allows users to mix and match components while maintaining strong defaults for common use cases.