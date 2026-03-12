# AGENTS.md - Agent Coding Guidelines for HarmonicPatterns

## Project Overview

HarmonicPatterns is a Python library for detecting harmonic trading patterns in financial market data (ZIGZAG patterns). It supports patterns: ABCD, Gartley, Bat, AltBat, ButterFly, Crab, DeepCrab, Shark, Cypher, and 5-O.

## Build, Lint, and Test Commands

### Installation
```bash
pip install -r requirements.txt
pip install -e .  # or python setup.py install
```

### Running the Project
```bash
# Run example scripts
python examples/run_detect.py

# Run Jupyter notebooks
jupyter notebook examples/HarmoCurrent.ipynb
```

### Testing
- **No formal test suite exists** - This project was developed interactively via Jupyter notebooks
- To add tests, create a `tests/` directory and use pytest
- Run single test: `pytest tests/test_patterns.py::test_function_name -v`

### Code Quality
- No linting tools currently configured
- Consider adding `flake8`, `black`, or `ruff` for code formatting/linting
- Manual code review recommended before committing

## Code Style Guidelines

### Imports
- Standard library imports first
- Third-party imports second (numpy, pandas, talib, mplfinance)
- One blank line between groups
- Example:
  ```python
  import os
  from typing import Optional, List, Tuple
  
  import numpy as np
  import pandas as pd
  import talib.abstract as ta
  import mplfinance as mpf
  ```

### Naming Conventions
- **Functions/variables**: snake_case (e.g., `get_zigzag`, `error_allowed`)
- **Classes**: PascalCase (e.g., `HarmonicDetector`)
- **Constants**: SCREAMING_SNAKE_CASE (e.g., `MAIN_FIBB_RATIOS`, `FIBB_RATIOS`)
- **Private methods**: prefix with underscore (e.g., `_internal_method`)

### Type Hints
- Use type hints for function signatures where clear
- Common patterns:
  ```python
  def kline_to_df(arr) -> pd.DataFrame:
  def is_eq(self, n: float, m: float, err: float = None, l_closed: bool = False, r_closed: bool = True) -> bool:
  ```
- Prefer explicit types over `Any`

### Function Guidelines
- Keep functions focused and small (under 100 lines preferred)
- Use descriptive parameter names
- Add docstrings for public APIs
- Example:
  ```python
  def get_zigzag(self, df: pd.DataFrame, period: int) -> List[List]:
      """
      Generate zigzag pattern from price data.
      
      Args:
          df: DataFrame with 'high' and 'low' columns
          period: Lookback period for zigzag calculation
          
      Returns:
          List of [direction, price, index] entries
      """
  ```

### Error Handling
- Use explicit exception handling for I/O operations
- Return `None` for "not found" cases (common pattern in this codebase)
- Validate inputs at function boundaries
- Example:
  ```python
  if len(df) < period:
      raise ValueError(f"DataFrame length {len(df)} less than period {period}")
  ```

### Class Structure
- Use `object` as base class if no inheritance: `class HarmonicDetector(object):`
- Group related methods together
- Keep `__init__` simple; defer complex setup to separate methods

### Code Patterns to Avoid
- Unused variables (e.g., `last_direction` often assigned but unused)
- Magic numbers - extract to named constants
- Deeply nested conditionals - consider early returns
- Long parameter lists - use dataclasses or dictionaries

### Data Structure Conventions
- Pattern lists: `[['H', price, idx], ['L', price, idx], ...]` where 'H'=high, 'L'=low
- Return dictionaries with descriptive keys
- Use pandas DataFrames for tabular data
- Use numpy arrays for numerical computations

### Fibonacci Ratios
The codebase uses these Fibonacci ratio constants:
- `MAIN_FIBB_RATIOS`: [0.618, 1.618]
- `SECOND_FIBB_RATIOS`: [0.786, 0.886, 1.13, 1.27]
- `ALT_FIBB_RATIOS`: [0.382, 0.5, 0.707, 1.41, 2.0, 2.24, 2.618, 3.14, 3.618]

### Pattern Detection Methods
Each pattern detector follows this signature:
```python
def detect_pattern_name(self, current_pat: list, predict: bool = False, predict_mode: str = 'direct')
```
- `current_pat`: List of 5 pattern points
- `predict`: Whether to predict the pattern
- `predict_mode`: 'direct' or 'reverse'

Returns `[direction, ret_dict]` on match, `None` otherwise.

### File Organization
```
src/
  harmonic_patterns.py   # Main detection logic
  harmonic_functions.py  # Duplicate/similar code (consider merging)
  settings.py           # Configuration
  __init__.py
examples/
  run_detect.py         # CLI entry point
  HarmoCurrent.ipynb    # Jupyter examples
res/
  *.png                 # Sample outputs
```

### Working with pandas DataFrames
- Expected columns: `ts`, `open`, `high`, `low`, `close`, `volume`
- Index should be datetime for plotting with mplfinance

### Git Conventions
- Create feature branches for new functionality
- Commit messages: clear, imperative mood ("Add Gartley pattern detection")
- Run basic smoke test before committing (verify imports work)
