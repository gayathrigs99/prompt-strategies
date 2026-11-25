# MP1: Prompt Strategy Comparison

This project compares four prompting strategies for extracting structured information from job postings:

- Zero-shot
- Few-shot
- Structured prompting
- Chain-of-thought (CoT)

Each strategy is run against the same 10 postings. The extracted company, role, and required years of experience are compared with a reference set.

## Project files

| File | Purpose |
|---|---|
| `prompts.ipynb` | Main notebook: prompts, API calls, scoring, and comparison table |
| `data/job_snippets.jsonl` | The 10 job postings to analyse |
| `data/golden_set.jsonl` | Reference extractions used for scoring |
| `requirements.txt` | Python packages required by the notebook |
| `mp1_writeup.md` | Short reflection on the results |
| `learner/MP1_Starter_Template.ipynb` | Original starter notebook |
| `learner/MP1_FAQ.md` | Project questions and scoring conventions |

## Requirements

- Python 3.10 or later (the notebook uses modern type annotations)
- Jupyter Notebook or JupyterLab
- An API key and API endpoint compatible with the OpenAI Python client

The notebook uses `gpt-4o-mini` for the four extraction strategies and `gpt-4o` as the scoring judge. The judge step makes an additional API request for each extraction, so a full run makes 40 extraction calls plus 40 judge calls. API charges depend on model pricing, token use, and your provider.

## Set up and run

Open a terminal in the project root (the directory containing this README), then create and activate a virtual environment if desired.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
python -m pip install -r requirements.txt
python -m pip install jupyter
```

Set the API credentials in the same terminal before launching Jupyter. Use the endpoint and key supplied by your provider; do not commit credentials to the repository.

```powershell
$env:OPENAI_API_KEY = "your-api-key"
$env:OPENAI_BASE_URL = "https://your-provider.example/v1"
jupyter notebook
```

On macOS or Linux, the equivalent environment setup is:

```bash
export OPENAI_API_KEY="your-api-key"
export OPENAI_BASE_URL="https://your-provider.example/v1"
jupyter notebook
```

In Jupyter, open `prompts.ipynb` and run the cells from top to bottom. The notebook locates the `data` directory by searching the current directory and its parents, so launch it from this project or a child directory. It sends extraction requests, parses the JSON responses, compares three fields with the golden set, asks the judge to score each result, and displays a summary table.

If your provider uses the standard OpenAI API endpoint, `OPENAI_BASE_URL` may be omitted. The notebook expects `OPENAI_API_KEY` to be set before its kernel starts.

## Interpreting the results

The final table has one row per strategy and these columns:

| Column | Meaning | How to read it |
|---|---|---|
| `Accuracy (mean of 3)` | Mean count of matching target fields per posting, from 0 to 3 | Higher is better. For example, 2.8 means 2.8 of the three fields matched on average across 10 postings. |
| `Parse rate` | Fraction of responses that could be parsed as JSON objects | 1.0 (100%) means every response parsed. This does not mean every extracted value is correct. |
| `Judge score` | Mean judge rating from 1 to 4 | Higher indicates better overall extraction quality according to the notebook's rubric. Treat small differences cautiously because the judge is itself a model. |
| `Total cost ($)` | Sum of estimated extraction costs for that strategy | The notebook calculates this from token usage and the rates defined in the notebook. It does not include the judge calls. Check those rates against your provider's current pricing. |
| `Latency p50 (s)` | Median extraction-call latency in seconds | Lower is faster for a typical call. It excludes the separate judge-call time. |

Accuracy scores only `company`, `role`, and `years_experience_required`. The notes field and `id` are not part of the exact-match score. Year values are normalised so values such as `5+` and `5` can match. Missing experience should be represented as `null` to match the reference for `j10`; a prompt that returns `0` will be scored as incorrect for that field. Review the prompts and edge cases before concluding that one strategy is better, and record changes to prompts or configuration when comparing runs.

The table is a comparison over this small dataset, not proof that a strategy will perform best on every task. Consider accuracy, parse rate, latency, and cost together, and inspect individual outputs for errors hidden by averages. Results can vary between runs because model responses and judge ratings may vary.

## Write up

Use `mp1_writeup.md` to record which strategy performed best and on what dimension, what surprised you, which strategy you would use first in your own domain, and what you would test next. Include any prompt changes that affect the comparison.
