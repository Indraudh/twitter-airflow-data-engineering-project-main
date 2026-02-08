# twitter-airflow-data-engineering-project

A small tutorial project that demonstrates a basic Airflow DAG which runs a simple Twitter ETL using Tweepy.

This repository contains a minimal example of an ETL pipeline that extracts tweets from a user's timeline, refines the data into a CSV, and is wired to an Airflow DAG so the pipeline can be scheduled.

## Project structure

- `twitter_dag.py` - An Airflow DAG that calls the ETL function once per day. It uses a `PythonOperator` to execute `run_twitter_etl` from `twitter_etl.py`.
- `twitter_etl.py` - Implements `run_twitter_etl()` which authenticates with the Twitter API using Tweepy, fetches recent tweets from a user timeline, builds a pandas DataFrame of selected fields, and writes `refined_tweets.csv` to the current working directory.
- `twitter_commands.sh` - (optional) helper shell commands used by the tutorial (review before running).

## What it does

- Authenticates to the Twitter API using credentials in `twitter_etl.py`.
- Fetches up to 200 most recent tweets from a hard-coded user (example: `@elonmusk`).
- Extracts the tweet text, author username, favorite count, retweet count, and creation timestamp.
- Writes the refined rows into `refined_tweets.csv`.
- The DAG (`twitter_dag`) schedules this ETL to run once per day when deployed to an Airflow environment.

## Prerequisites

- Python 3.8+ (recommended)
- pip
- An Airflow installation to run the DAG (optional if you only want to run the ETL locally)
- A Twitter developer account and API credentials (API key, API secret key, Access token, Access token secret)

Notes about Airflow: installing and configuring Airflow is outside the scope of this README. If you want to run the DAG, ensure Airflow is installed and your `dags_folder` includes `twitter_dag.py`.

## Quick setup (local run)

1. Clone the repo and change into the project folder.
2. Create and activate a virtual environment.

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
```

3. Install required Python packages.

```powershell
pip install tweepy pandas s3fs
```

4. Configure Twitter credentials.

Open `twitter_etl.py` and set the following variables near the top of `run_twitter_etl()`:

- `access_key` (API key)
- `access_secret` (API secret key)
- `consumer_key` (Access token)
- `consumer_secret` (Access token secret)

The current code uses hard-coded variables. For production use, move credentials to environment variables or a secrets manager.

5. Run the ETL function directly (quick test).

```powershell
python -c "from twitter_etl import run_twitter_etl; run_twitter_etl()"
```

After running, `refined_tweets.csv` will be created in the working directory.

## Deploying to Airflow

1. Ensure Airflow is installed and initialized.
2. Copy `twitter_dag.py` into your Airflow `dags` directory (or point your `dags_folder` to this repo directory).
3. Make sure the Python environment that runs Airflow has the same dependencies installed and that `twitter_etl.py` is importable from the DAG.
4. Start the Airflow webserver and scheduler. The DAG `twitter_dag` will appear in the UI and can be triggered or left on its daily schedule.

## Notes & next steps

- The ETL currently writes a local CSV. The import of `s3fs` indicates an intent to write to S3 — you can replace `df.to_csv('refined_tweets.csv')` with `df.to_csv('s3://bucket/path/refined_tweets.csv', index=False)` and ensure AWS credentials are configured.
- Improve credentials handling by using environment variables or Airflow connections/secrets.
- Add error handling and logging to `run_twitter_etl()` for reliability.
- Add unit tests for the data transformation logic.

## Troubleshooting

- If Tweepy raises authentication errors, verify API keys and tokens.
- If Airflow cannot import `twitter_etl`, ensure that the DAG's Python path includes the repo and that dependencies are installed for the Airflow environment.

## License

This repo is a tutorial/example. There is no license file included.

---
If you'd like, I can also:

- Add a `requirements.txt` with the minimal packages used.
- Move credential loading to environment variables and update the code.
- Add a simple README badge and a short example CSV.
