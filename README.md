# GooglePubSub# PubSub Stream to BigQuery Project

A data engineering project that demonstrates streaming data from Google Cloud Pub/Sub to BigQuery with data transformation capabilities. This project simulates IRCTC (Indian Railway) customer data streaming pipeline.

## 📋 Overview

This project implements an end-to-end data streaming pipeline that:
1. Generates mock IRCTC customer data
2. Publishes data to Google Cloud Pub/Sub
3. Transforms and enriches the data using a UDF (User Defined Function)
4. Loads the transformed data into BigQuery for analytics

## 🏗️ Architecture

```
Mock Data Generator → Pub/Sub Topic → Data Transformation (UDF) → BigQuery Table
```

## 📁 Project Structure

```
PubSub-Stream-To-BigQuery-Project/
├── irctc_mock_data_to_pubsub.py    # Script to generate and publish mock data to Pub/Sub
├── transform_udf.py                 # UDF for data transformation and enrichment
├── bigquery_create_table.sql        # SQL script to create BigQuery destination table
└── README.md                         # Project documentation
```

## 🔧 Prerequisites

- Python 3.7 or higher
- Google Cloud Platform (GCP) account with billing enabled
- GCP Project with the following APIs enabled:
  - Cloud Pub/Sub API
  - BigQuery API
  - Cloud Dataflow API (if using Dataflow for streaming)
- Google Cloud SDK installed and configured
- Service account with appropriate permissions:
  - Pub/Sub Publisher
  - BigQuery Data Editor
  - BigQuery Job User

## 📦 Installation

1. **Clone the repository**
   ```bash
   git clone <repository-url>
   cd PubSub-Stream-To-BigQuery-Project
   ```

2. **Install required Python packages**
   ```bash
   pip install google-cloud-pubsub google-cloud-bigquery
   ```

3. **Set up Google Cloud authentication**
   ```bash
   gcloud auth application-default login
   ```
   Or set the `GOOGLE_APPLICATION_CREDENTIALS` environment variable to point to your service account key file.

## ⚙️ Configuration

### Update Project Settings

Before running the scripts, update the following in `irctc_mock_data_to_pubsub.py`:

```python
project_id = "your-gcp-project-id"  # Replace with your GCP project ID
topic_id = "irctc-data"              # Pub/Sub topic name
```

### Create Pub/Sub Topic

Create the Pub/Sub topic in your GCP project:

```bash
gcloud pubsub topics create irctc-data --project=your-gcp-project-id
```

### Create BigQuery Dataset and Table

1. Create the BigQuery dataset:
   ```bash
   bq mk --dataset your-gcp-project-id:irctc_dwh
   ```

2. Run the SQL script to create the table:
   ```bash
   bq query --use_legacy_sql=false < bigquery_create_table.sql
   ```

## 🚀 Usage

### 1. Generate and Publish Mock Data

Run the script to generate mock IRCTC customer data and publish it to Pub/Sub:

```bash
python irctc_mock_data_to_pubsub.py
```

This script will:
- Generate 20 mock customer records (configurable)
- Publish each record as a JSON message to the Pub/Sub topic
- Display the published message IDs

### 2. Data Transformation

The `transform_udf.py` file contains a transformation function that:
- Cleans and validates data (name capitalization, email normalization)
- Enriches data with loyalty status based on loyalty points
- Converts timestamps to ISO format
- Calculates account age in days
- Handles missing or invalid values with defaults

This UDF can be used in:
- Cloud Dataflow pipelines
- BigQuery User-Defined Functions
- Apache Beam transformations

### 3. Data Schema

The BigQuery table schema includes the following fields:

| Field | Type | Description |
|-------|------|-------------|
| row_key | STRING | Unique identifier for each record |
| name | STRING | Customer name |
| age | INT64 | Customer age |
| email | STRING | Customer email address |
| join_date | DATE | Account creation date |
| last_login | TIMESTAMP | Last login timestamp |
| loyalty_points | INT64 | Customer loyalty points |
| account_balance | FLOAT64 | Account balance |
| is_active | BOOL | Account active status |
| inserted_at | TIMESTAMP | Record insertion timestamp |
| updated_at | TIMESTAMP | Record update timestamp |
| loyalty_status | STRING | Loyalty tier (Platinum/Standard) |
| account_age_days | INT64 | Account age in days |

## 🔄 Data Flow

1. **Data Generation**: Mock IRCTC customer data is generated with fields like name, age, email, join_date, loyalty_points, etc.

2. **Pub/Sub Publishing**: Each record is serialized to JSON and published as a message to the Pub/Sub topic.

3. **Data Transformation**: The UDF processes each message to:
   - Clean and normalize data
   - Enrich with calculated fields (loyalty_status, account_age_days)
   - Handle data quality issues

4. **BigQuery Loading**: Transformed data is loaded into the BigQuery table for analytics and reporting.

## 🛠️ Customization

### Adjust Mock Data Volume

In `irctc_mock_data_to_pubsub.py`, modify the number of records:

```python
mock_data = generate_mock_data(20)  # Change 20 to desired number
```

### Modify Data Transformation

Edit `transform_udf.py` to add custom transformation logic according to your requirements.

### Update Table Schema

Modify `bigquery_create_table.sql` to add or remove fields as needed.

## 📝 Notes

- The project uses UTC timestamps for consistency
- Default values are set for missing fields to ensure data quality
- The transformation UDF handles edge cases and invalid data gracefully
- Ensure your GCP project has sufficient quotas for Pub/Sub and BigQuery operations

## 🔐 Security Considerations

- Never commit service account keys or credentials to the repository
- Use IAM roles with least privilege principle
- Enable audit logging for production environments
- Consider using Secret Manager for sensitive configuration

## 📄 License

This project is provided as-is for educational and demonstration purposes.

## 🤝 Contributing

Contributions are welcome! Please feel free to submit a Pull Request.

## 📧 Contact

For questions or issues, please open an issue in the repository.

