# MP1 Project Guide

This project compares four prompting strategies for extracting structured information from job-posting snippets.

The notebook extracts three fields:

- `company`
- `role`
- `years_experience_required`

## Project Layout

```text
Source/
├── data/
│   ├── golden_set.jsonl
│   └── job_snippets.jsonl
├── learner/
│   ├── MP1_FAQ.md
│   └── mp1_prompt_lab.ipynb
├── mp1_comparison.md
├── mp1_writeup.md
├── requirements.txt
└── README_GUIDE.md
```

## Requirements

You need:

- Python 3.10 or newer
- VS Code with the Jupyter extension, or another Jupyter environment
- An OpenAI-compatible API key

The project uses `gpt-4o-mini` for the four main strategies and `gpt-4o` for judging results.

## Setup

Open a terminal in the `Source` folder:

```powershell
cd "D:\MyRepository\MyAgenticAI-&-RAG\Pravartak\Mini-Project-W5\Source"
```

Create and activate a virtual environment if you do not already have one:

```powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

Install the dependencies:

```powershell
python -m pip install -r requirements.txt
```

## API Configuration

Create a `.env` file in the `Source` folder. For the standard OpenAI-compatible endpoint:

```env
OPENAI_API_KEY=your_api_key_here
```

For OpenRouter, use:

```env
OPENROUTER_API_KEY=your_api_key_here
OPENROUTER_BASE=https://openrouter.ai/api/v1
```

Do not commit `.env` or expose the API key in the notebook.

## Run The Notebook

1. Open `learner/mp1_prompt_lab.ipynb` in VS Code.
2. Select the Python interpreter from the virtual environment.
3. Select the same interpreter as the notebook kernel.
4. Run the cells from top to bottom.
5. Confirm that the data-loading cell reports 10 snippets and 10 golden entries.
6. Run the prompt strategy cell.
7. Run the async batching cell to define `run_one` and `run_all`.
8. Run the `Run it` cell. This sends 40 calls: 10 snippets multiplied by 4 strategies.
9. Run the scoring function cell.
10. Run the scoring cell. This sends up to 40 additional judge calls.
11. Run the comparison-table cell.

In a notebook, use top-level `await` as shown in the project. Do not wrap notebook calls in `asyncio.run()`.

## What The Pipeline Does

The notebook follows this sequence:

1. Loads the job snippets and golden answers.
2. Builds prompts for four strategies:
   - zero-shot
   - few-shot
   - structured / role-based
   - chain-of-thought
3. Runs all strategy and snippet combinations concurrently with `asyncio.gather`.
4. Parses JSON responses, including responses wrapped in Markdown code fences.
5. Records response text, parsed extraction, token usage, cost, and latency.
6. Scores each extraction against the golden set.
7. Uses `gpt-4o` as an additional judge.
8. Builds a comparison table.

## Understanding The Results

The comparison table contains these columns:

| Column | Meaning |
|---|---|
| `Accuracy (mean of 3)` | Average number of correct fields out of the three target fields. A score of `3.0` means all fields were correct on average. |
| `Parse rate` | Percentage of responses that were successfully parsed as JSON objects. `1.0` means 100%. |
| `Judge score` | Average score from 1 to 4 assigned by the LLM judge. |
| `Total cost ($)` | Total estimated API cost for that strategy. Costs should be displayed to six decimal places. |
| `Latency p50 (s)` | Median response time in seconds. |

A useful interpretation usually considers all dimensions together:

- Prefer higher accuracy when correctness is the main goal.
- Prefer a higher parse rate when downstream code needs reliable JSON.
- Consider judge score as a second evaluation signal, not a replacement for the golden-set score.
- Consider cost and latency when the pipeline will run frequently or at scale.
- A strategy that is slightly more accurate but much slower or more expensive may not be the best production choice.

## Current Comparison Summary

The latest recorded run produced these results:

| Strategy | Accuracy | Parse rate | Judge score | Total cost | Median latency |
|---|---:|---:|---:|---:|---:|
| Few-shot | 3.0 | 1.0 | 4.0 | `$0.000494` | 4.093032 s |
| Structured | 3.0 | 1.0 | 3.8 | `$0.000382` | 4.389013 s |
| Zero-shot | 2.6 | 1.0 | 3.6 | `$0.000362` | 3.489297 s |
| Chain-of-thought | 2.7 | 1.0 | 3.8 | `$0.001054` | 5.967515 s |

In that run, few-shot and structured tied for accuracy. Few-shot had the highest judge score, while structured gave the strongest accuracy-to-cost balance. Zero-shot was fastest and cheapest, and chain-of-thought was the slowest and most expensive.

## Output Files

- `mp1_comparison.md` contains the formatted comparison table and recommendation.
- `mp1_writeup.md` contains the four-question reflection.

Update these files after rerunning the notebook if the measured results change.

## Common Problems

### API key error

Check that `.env` is in the `Source` folder and that the variable name matches the selected endpoint:

- `OPENAI_API_KEY`
- or `OPENROUTER_API_KEY` and `OPENROUTER_BASE`

Restart the notebook kernel after changing environment variables.

### Data file not found

The notebook expects to be opened from the `learner` folder layout and uses:

```python
DATA_DIR = Path('../data')
```

Keep the notebook under `Source/learner/` or update `DATA_DIR`.

### Event loop error

In Jupyter, run:

```python
results = await run_all()
```

Do not use `asyncio.run(run_all())` inside a notebook.

### Invalid JSON response

The parser handles plain JSON, Markdown fences, and JSON after explanatory text. If parsing still fails, inspect `raw_response` in the result row and adjust the prompt or parser.

### Unexpected costs

The main experiment uses 40 model calls, and judging adds up to 40 more. Avoid rerunning the full notebook repeatedly while debugging. Check that the main model is `gpt-4o-mini` and that the judge uses `gpt-4o`.
