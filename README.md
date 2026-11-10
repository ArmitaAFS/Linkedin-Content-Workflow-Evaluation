# LinkedIn Content Workflow Evaluation

A small experimental study comparing three LLM-based workflows for generating LinkedIn posts

## Overview

The project evaluates whether iterative reflection and externally computed structural feedback can improve generated LinkedIn content.

### Compared workflows

| ID | Workflow | Description |
|---|---|---|
| A | Initial Draft | Generates a post directly from the user prompt. |
| B | Reflection | Generates an initial draft, receives qualitative feedback, and revises the draft. |
| C | Reflection + Python Validation | Generates an initial draft, calculates structural quality signals with Python, passes those signals into qualitative reflection, and revises the draft. |

For each prompt, the same initial draft is used as the starting point for workflows B and C. The two revision paths are executed independently.

## Experimental setup

- **Prompts:** 8
- **Candidates:** 3 per prompt
- **Total evaluated candidates:** 24
- **Generation/evaluation model:** `gpt-4o` (configured in the notebook)
- **API interface:** OpenAI-compatible endpoint provided by GapGPT
- **Structural constraints:** maximum 500 characters and 3–8 hashtags
- **Evaluation:** one candidate is evaluated at a time by an LLM judge using a seven-criterion rubric

### LLM-as-a-Judge rubric

| Criterion | Maximum points |
|---|---:|
| Relevance | 25 |
| Value | 20 |
| Originality | 15 |
| Hook | 15 |
| Readability | 10 |
| Tone and language | 10 |
| Call to action | 5 |
| **Total** | **100** |

The judge prompt does not provide workflow identities and asks the model to evaluate each post independently. This reduces potential method-label bias, but does not eliminate other evaluator biases.

The prompts and generated posts are experimental examples. In particular, the prompt about announcing a new role at Microsoft is a test prompt; its generated text must not be interpreted or reused as a factual claim about the project author.

## Results

Mean final scores across the eight prompts:

| Workflow | Mean score |
|---|---:|
| Initial Draft (A) | 85.63 |
| Reflection (B) | 87.75 |
| Reflection + Python Validation (C) | 87.88 |

Paired mean differences:

- Reflection − Initial Draft: **+2.13 points**
- Reflection + Python Validation − Initial Draft: **+2.25 points**
- Reflection + Python Validation − Reflection: **+0.13 points**

All 24 candidates passed the implemented character-count and hashtag-count checks. Since this sample contains no structurally invalid candidates, it does not establish the validator's ability to detect violations across a broader range of inputs.

## Repository structure

```text
linkedin-content-workflow-evaluation/
├── workflow_evaluation.ipynb
├── data/
│   ├── generated_candidates.json
│   └── evaluation_scores.csv
├── .env.example
├── .gitignore
├── requirements.txt
└── README.md
```

## Setup

Python 3.10+ is recommended.

```bash
python -m venv .venv
```

Activate the environment, then install dependencies:

```bash
pip install -r requirements.txt
```

Create a `.env` file based on `.env.example` and add your API key:

```env
GAPGPT_API_KEY=your_api_key_here
```

Never commit `.env` or expose API keys in notebooks, screenshots, logs, or Git history.

Open `workflow_evaluation.ipynb` in Jupyter or VS Code. The generation and LLM-judge cells make API calls. Rerunning them may incur costs and will produce new outputs; the files in `data/` preserve the collected candidate outputs and final-score summary from this experiment.

## Limitations

- The experiment contains only eight prompts; findings are exploratory and should not be generalized without a larger sample.
- LLM-as-a-Judge scores are not equivalent to human ground-truth judgments and may reflect model-specific preferences or systematic biases.
- Hiding workflow identities from the judge reduces method-label bias, but does not remove style, verbosity, rubric, or position effects.
- All 24 candidates passed the structural checks, so the dataset does not test the validator on negative examples.
- The average difference between workflows B and C is only 0.13 points; this experiment does not demonstrate a meaningful qualitative advantage for adding Python validation over reflection alone.
- Generated factual claims still require independent verification.

## Future work

- Expand the prompt set and include more diverse topics and writing requirements.
- Add intentionally invalid test cases to test structural validation behavior.
- Use randomized presentation order where pairwise comparisons are performed.
- Include human evaluation and inter-rater agreement analysis.
- Verify factual claims independently.

## License

Add a license file before public distribution if you want to specify reuse permissions.
