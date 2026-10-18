# Recursive Digestion Framework

Code for the MS thesis "A Recursive Digestion Framework for Context Aware Language Understanding", Muhammad Fahad, NED University of Engineering and Technology (DS-020/2025). Supervisor: Dr. Engr. Maria Andleeb Siddiqui.

The notebook compares a single draft baseline against a recursive refinement pipeline on long government reports, and scores both with the same metrics.

## How the pipeline works

1. Baseline: one summary draft per document. Inputs longer than 20,000 characters use map reduce with 18,000 character chunks, shorter inputs use a single pass.
2. Clustering: the document is split into units, embedded with all-MiniLM-L6-v2, and grouped with HDBSCAN.
3. Refinement: between 3 and 8 loops, one cluster at a time. Each proposed addition must carry a quote from the source and is rejected if the quote overlap is below 0.75.
4. Synthesis (optional): one call that merges the draft and the verified additions into prose. Controlled by the `SYNTHESIZE` flag. If the output is shorter than 20% of the draft, the draft is kept.

## Evaluation

* Dataset: `ccdv/govreport-summarization`, test split, 30 documents (10 short, 10 medium, 10 long by character count), sampled with seed 42.
* Metrics: FActScore style claim verification, claim density, semantic coverage, BERTScore against the reference summary.
* Statistics: Wilcoxon signed rank test on paired baseline and framework scores.

## Models

Run through Ollama, one model per session: `qwen2.5:3b`, `qwen2.5:7b`, `llama3.1:8b`. Temperature 0.0, context 16384.

## Requirements

* Google Colab with a GPU (embeddings load on CUDA) and Google Drive access.
* Python packages: see `requirements.txt`. Ollama is installed by the first cell.

## How to run

1. Open `source_code.ipynb` in Colab.
2. In Cell 2 set `PIPELINE_MODEL` and `SYNTHESIZE`. These are the only per run settings.
3. Run all cells. Use `EVAL_NUM_TOPICS = 1` first for a quick check.
4. Results are written to `thesis_pipeline/runs/<model>_synth_<on|off>/outputs` in Google Drive. An interrupted run resumes from `results_incremental.pkl`.

## Runs

The run tag is `<model>_synth_on` or `<model>_synth_off`, so every model and synthesis setting gets its own folder.

## Citation

Muhammad Fahad. A Recursive Digestion Framework for Context Aware Language Understanding. MS thesis, NED University of Engineering and Technology, 2025.
