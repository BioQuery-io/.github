# Contributing to BioQuery

Thank you for your interest in contributing to BioQuery! This guide will help you get started.

## Table of Contents

- [Getting Started](#getting-started)
- [Development Setup](#development-setup)
- [Adding a New Analysis Type](#adding-a-new-analysis-type)
- [Adding Routing Examples](#adding-routing-examples)
- [Code Style](#code-style)
- [Testing](#testing)
- [Pull Request Guidelines](#pull-request-guidelines)

## Getting Started

1. Fork the repository you want to contribute to
2. Clone your fork locally
3. Create a feature branch: `git checkout -b feature/your-feature`
4. Make your changes
5. Run tests: `uv run pytest`
6. Commit with a clear message: `git commit -m "feat: add new feature"`
7. Push to your fork: `git push origin feature/your-feature`
8. Open a Pull Request

## Development Setup

### API (Python)

```bash
cd api/

# Install dependencies with uv
uv sync

# Run locally
uv run uvicorn app.main:app --reload --port 8000

# Run tests
uv run pytest -v
```

### Frontend (TypeScript)

```bash
cd frontend/

# Install dependencies
npm install

# Run locally
npm run dev
```

## Adding a New Analysis Type

The Analysis Registry (`api/app/analysis/registry.py`) is the single source of truth for all analysis types. Adding a new analysis type now requires only **3 steps**:

### Step 1: Create the Runner Function

Create a new file or add to an existing one in `api/app/analysis/`:

```python
# api/app/analysis/your_analysis.py

from app.schemas.card import QueryCard

async def run_your_analysis(
    question: str,
    parsed: "ParsedQuery",
    gene: str,
    cancer_types: list[str],
    start_time: float,
    user_id: str,
    progress: "Progress",
) -> QueryCard:
    """
    Run your analysis type.

    Args:
        question: Original natural language question
        parsed: Parsed query with analysis_type, gene, cancer_types
        gene: Gene symbol (e.g., "TP53")
        cancer_types: List of TCGA codes (e.g., ["BRCA", "LUAD"])
        start_time: Query start time for latency tracking
        user_id: User ID for attribution
        progress: Progress reporter for streaming updates

    Returns:
        QueryCard with results, figure, statistics
    """
    # 1. Emit progress
    await progress.emit("fetching", 20, "Fetching data from BigQuery...")

    # 2. Fetch data from BigQuery
    from app.services.bigquery import get_gene_expression
    data = await get_gene_expression(gene, cancer_types)

    # 3. Run statistics
    await progress.emit("analyzing", 50, "Running statistical analysis...")
    # ... your statistical code ...

    # 4. Create visualization
    await progress.emit("visualizing", 80, "Creating figure...")
    # ... your plotly figure code ...

    # 5. Return QueryCard
    return QueryCard(
        id=generate_card_id(),
        query=Query(original=question, parsed=parsed),
        result=Result(...),
        figure=Figure(...),
        # ... other fields
    )
```

### Step 2: Add to the Registry

Add your analysis type to `api/app/analysis/registry.py`:

```python
ANALYSIS_REGISTRY = {
    # ... existing entries ...

    "your_analysis": {
        "runner": "run_your_analysis",
        "aliases": ["your_alias"],  # Optional aliases
        "consultants": ["statistics", "genomics"],  # Domain experts to consult
        "method": "wilcoxon_rank_sum",  # Statistical method name
        "description": "Description for documentation",
        "specialized": False,  # True if skip planner
    },
}
```

### Step 3: Export from Module

Add the export in `api/app/analysis/__init__.py`:

```python
from app.analysis.your_analysis import run_your_analysis

__all__ = [
    # ... existing exports ...
    "run_your_analysis",
]
```

### That's It!

The registry automatically:
- Makes the runner available to the executor
- Maps consultant requirements
- Provides the statistical method name
- Handles aliases

No other files need modification.

## Adding Routing Examples

Routing examples help the LLM understand what analysis type to use for different queries. Add examples to `api/data/routing_examples.jsonl`:

```json
{"question": "Is BRCA1 higher in ovarian cancer vs breast cancer?", "analysis_type": "differential_expression", "gene": "BRCA1", "cancer_types": ["OV", "BRCA"]}
{"question": "Does high TP53 expression predict survival in lung cancer?", "analysis_type": "survival_analysis", "gene": "TP53", "cancer_types": ["LUAD"]}
```

Each line is a JSON object with:
- `question`: Example natural language query
- `analysis_type`: Correct analysis type
- `gene`: Gene symbol (if applicable)
- `cancer_types`: TCGA codes (if applicable)

## Code Style

### Python (API, SDK)

```bash
# Format with Black
black app/

# Sort imports with isort
isort app/

# Type check with mypy
mypy app/

# Lint with ruff
ruff check app/
```

### TypeScript (Frontend, Docs)

```bash
# Format with Prettier
npm run format

# Lint with ESLint
npm run lint

# Type check
npm run type-check
```

## Testing

### Running Tests

```bash
# Run all tests
uv run pytest

# Run with verbose output
uv run pytest -v

# Run specific test file
uv run pytest tests/test_analysis_runners.py

# Run with coverage
uv run pytest --cov=app
```

### Writing Tests

Tests should go in `api/tests/` and follow the naming convention `test_*.py`:

```python
# tests/test_your_analysis.py

import pytest
from app.analysis import run_your_analysis

@pytest.mark.asyncio
async def test_your_analysis_basic():
    """Test basic functionality."""
    card = await run_your_analysis(
        question="Your test question",
        parsed=...,
        gene="TP53",
        cancer_types=["BRCA"],
        start_time=0,
        user_id="test",
        progress=NoOpProgress(),
    )

    assert card.result is not None
    assert card.figure is not None
```

## Pull Request Guidelines

### Before Submitting

1. Run all tests: `uv run pytest`
2. Run formatters: `black app/ && isort app/`
3. Run type checker: `mypy app/`
4. Update documentation if needed

### PR Checklist

- [ ] Code follows existing style
- [ ] Tests added for new functionality
- [ ] All tests pass
- [ ] Documentation updated (if applicable)
- [ ] Commit messages follow Conventional Commits

### Commit Messages

We follow [Conventional Commits](https://www.conventionalcommits.org/):

- `feat:` - New features
- `fix:` - Bug fixes
- `docs:` - Documentation changes
- `style:` - Code style changes (formatting, etc.)
- `refactor:` - Code refactoring
- `test:` - Adding or updating tests
- `chore:` - Maintenance tasks

Examples:
```
feat: add immunotherapy response analysis
fix: handle missing p-value in survival analysis
docs: update CONTRIBUTING guide
refactor: consolidate runner mappings into registry
```

## Questions?

- Open a GitHub issue
- Email: hello@bioquery.io
- Documentation: https://docs.bioquery.io
