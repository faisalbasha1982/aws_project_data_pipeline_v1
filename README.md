# 🛠 Building a Serverless Data Pipeline on AWS with S3, Lambda, Glue, and QuickSight

With the exponential growth in data sources, serverless data pipelines are becoming the go-to architecture for scalable, cost-efficient analytics. In this post, we'll walk through a robust, event-driven data pipeline using **AWS S3**, **Lambda**, **Glue**, and **QuickSight**. We'll use real-world sample datasets: IoT sensor data, user logs, and sales records.

---

## 🔧 Architecture Overview

This pipeline follows a structured **Extract–Transform–Load (ETL)** pattern, all within a serverless ecosystem:

### 🔄 Flow Breakdown:

1. **Upload CSV File to S3**  
   The pipeline begins when raw CSV files (e.g., from IoT sensors) are uploaded to an S3 bucket (`csv-raw-data`).

2. **Trigger Lambda Function**  
   An S3 event triggers a Lambda function that parses and pre-processes the raw data. This includes schema validation, column cleanup, and format normalization.

3. **Store Cleaned Data**  
   The Lambda function saves the cleaned records into a secondary S3 bucket (`csv-processed-data`).

4. **AWS Glue Crawler**  
   A Glue Crawler scans the processed files and infers the schema to update the **Glue Data Catalog**.

5. **Glue Job (Transform + Load)**  
   A Python-based AWS Glue Job reads data from the catalog, applies necessary transformations (e.g., date parsing, type casting), and writes the final output to `csv-final-data`.

6. **Data Visualization with QuickSight**  
   QuickSight connects to the final dataset stored in S3 and generates interactive dashboards and insights.

---

## 📦 Key AWS Resources (All Provisioned with Terraform)

- 3 S3 Buckets:
  - `csv-raw-data`
  - `csv-processed-data`
  - `csv-final-data`
- IAM Roles for Lambda and Glue with least-privilege policies
- Lambda Function (Python runtime)
- Glue Crawler + Catalog Table
- Glue Job (Python script)
- QuickSight Dataset (Manual connection after IAM and source config)

---

## 🧪 Sample Datasets Used

Each CSV file contains **80+ records** to mimic realistic data sources:

- `iot_sensor_data.csv`: Sensor ID, timestamp, temperature, humidity  
- `user_logs.csv`: User ID, action, device type, location  
- `sales_records.csv`: Transaction ID, item, quantity, region, timestamp  

These datasets are included in the test bundle and allow you to validate the pipeline end to end.

---

## 🧰 Terraform Setup

We used a modular Terraform configuration to ensure reusable infrastructure. Bucket names are parameterized via variables. Scripts are grouped under the `terraform/` and `lambda/` directories in the provided package.

```bash
# Deploy Infrastructure
terraform init
terraform apply

# Upload CSV to trigger pipeline
aws s3 cp ./csv/iot_sensor_data.csv s3://<your-raw-data-bucket>/
