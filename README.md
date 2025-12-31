# Benchmark Log Analysis Scripts

This set of scripts is designed to process and analyze log files from benchmark runs. The process is automated by the `run_analysis.py` script, which orchestrates the entire workflow from raw log processing to URL extraction.

## Directory Structure

For the scripts to function correctly, your benchmark data should be organized as follows:

```
.
├── cursor_benchmarks/
│   ├── a_benchmark_run_name_1/
│   │   ├── run_1
│   │   ├── run_2
│   │   └── ...
│   └── another_benchmark_run_2/
│       ├── run_1
│       └── ...
└── scripts/
    ├── run_analysis.py
    ├── process_and_organize_logs.py
    ├── create_csv_from_logs.py
    ├── extract_clone_urls.py
    └── extract_logs.py
```

- **`cursor_benchmarks/`**: This directory should contain all your benchmark run folders.
- **`a_benchmark_run_name_1/`**: Each subdirectory inside `cursor_benchmarks` represents a single benchmark and should contain the raw `run_*` log files.

## How to Run the Analysis

To process a new benchmark run, you only need to execute the `run_analysis.py` script, passing the path to the specific benchmark directory you want to analyze.

### Usage

```bash
python scripts/run_analysis.py <path_to_benchmark_dir>
```

### Example

If you want to analyze the benchmark data located in `cursor_benchmarks/gemini-3-pro_librepods_20251229_123155`, you would run:

```bash
python scripts/run_analysis.py cursor_benchmarks/gemini-3-pro_librepods_20251229_123155
```

The script will then perform all the necessary steps automatically.

## The Analysis Pipeline

The `run_analysis.py` script executes the following steps:

1.  **Process and Organize Logs (`process_and_organize_logs.py`)**:
    - For each `run_*` file in the target benchmark directory, it creates a `run_*_processed` subdirectory.
    - It runs `extract_logs.py` on each `run_*` file, which generates `last_line.txt`, `thinking.txt`, and `tool_calls.jsonl` inside the corresponding `_processed` directory.

2.  **Create CSV (`create_csv_from_logs.py`)**:
    - It aggregates the data from all the `_processed` directories into a single CSV file.
    - The output CSV is saved in a new `csv/` directory at the project root.

3.  **Extract URLs (`extract_clone_urls.py`)**:
    - It parses the generated CSV file.
    - It searches for `git clone` URLs within the `last_line`, `thinking`, and `tool_calls` columns.
    - It saves a JSON file containing a dictionary that maps each `run_id` to a list of unique URLs found. This JSON is also saved in the `csv/` directory.

## Output Files

After running the analysis, you will find the following output files:

-   **Inside each `.../run_*_processed/` directory**:
    -   `last_line.txt`: The last line of the log.
    -   `thinking.txt`: The concatenated 'thinking' deltas.
    -   `tool_calls.jsonl`: A JSONL file of all tool calls.

-   **Inside the `csv/` directory**:
    -   `<benchmark_name>.csv`: A CSV file summarizing the data for all runs in that benchmark.
    -   `<benchmark_name>_url.json`: A JSON file containing the extracted `git clone` URLs for each run.
