# Music Streaming ETL with Airflow and Redshift

Batch ETL pipeline that moves music streaming data from Amazon S3 into Amazon Redshift Serverless, orchestrated by Apache Airflow on Amazon MWAA.

![Architecture diagram](ETL-with-Airflow/architecture/overal_architecture.PNG)

## How it works

The DAG (`music_streaming_pipeline_mwaa_v4`) runs four tasks in order:

1. **Extract**: reads new streaming events from S3 incrementally, using a stored timestamp marker so each run only picks up data it hasn't processed yet.
2. **Validate**: checks the incoming records before they move on.
3. **Transform**: enriches streams with track metadata and computes genre-level and hourly KPIs, written back to S3 as Parquet.
4. **Load**: copies the KPIs from S3 into Redshift staging tables, then into fact tables.

![Airflow DAG run](ETL-with-Airflow/images/DAG_run.png)

## Tech stack

Apache Airflow (Amazon MWAA) · Amazon S3 · Amazon Redshift Serverless · Python (pandas, boto3, pyarrow)

## Project structure

```
ETL-with-Airflow/
├── dags/           # DAG definition plus extract, validate, transform and load modules
├── architecture/   # Architecture diagram
├── images/         # Screenshots
└── requirements.txt
```

## Configuration

Bucket names and paths are set in `ETL-with-Airflow/dags/config.py`. Before running, set:

- `REDSHIFT_COPY_IAM_ROLE`: environment variable with the ARN of the IAM role Redshift uses for `COPY` from S3
- `redshift_conn_id`: an Airflow connection to your Redshift Serverless workgroup
