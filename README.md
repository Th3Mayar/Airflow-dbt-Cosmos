# **Data Engineering Pipeline with Airflow, Snowflake, dbt, and Snowpark**

## **Overview**
This project demonstrates the implementation of a modern, automated data pipeline using cutting-edge tools in data engineering. The pipeline includes:

1. **Data ingestion** into Snowflake.
2. **Data transformation** using dbt (Data Build Tool).
3. **Advanced analysis** using Snowpark (Python in Snowflake).
4. **Workflow orchestration** with Apache Airflow.
5. **Automation and modularity** with Cosmos.

---

## **Architecture**
The pipeline follows a modular architecture comprising the following stages:

1. **Raw Data Ingestion**: Data is loaded into Snowflake's raw tables.
2. **Transformation Layer**: dbt transforms raw data into meaningful datasets.
3. **Analysis Layer**: Snowpark runs advanced analytics using Python directly in Snowflake.
4. **Orchestration Layer**: Airflow automates and schedules the entire process.

---

## **Key Components**
### **1. Apache Airflow**
- **Purpose**: Orchestration of workflows.
- **Key Code**: `pipeline_dag.py` defines the Directed Acyclic Graph (DAG) and schedules tasks.

### **2. Snowflake**
- **Purpose**: Cloud-based data warehouse for storing and processing data.
- **Key Scripts**:
  - `create_table.sql`: Creates raw data tables.
  - `load_data.sql`: Loads sample data into Snowflake.

### **3. dbt**
- **Purpose**: Handles SQL-based transformations and data testing.
- **Key Files**:
  - `dbt_project.yml`: Configures the dbt project.
  - `profiles.yml`: Defines the Snowflake connection.
  - `models/my_model.sql`: SQL transformation logic.

### **4. Snowpark**
- **Purpose**: Executes advanced Python-based analytics within Snowflake.
- **Key Script**: `analysis.py`.

---

## **Setup and Execution**

### **1. Prerequisites**
- A Snowflake account.
- Apache Airflow with Astronomer CLI installed.
- dbt installed and configured for Snowflake.
- Docker installed to run Airflow locally.

### **2. Pipeline Setup**
#### **a) Snowflake**
1. Create a database and schema in Snowflake.
2. Run the SQL scripts to create tables and load data:
   - `create_table.sql`
   - `load_data.sql`

#### **b) dbt**
1. Configure `profiles.yml` with Snowflake connection details.
2. Define SQL models in the `models/` directory.
3. Run transformations:
   ```bash
   dbt run
   ```

#### **c) Airflow**
1. Define the DAG in `dags/pipeline_dag.py`.
2. Configure Snowflake and dbt connections in the Airflow UI.
3. Start the Airflow environment:
   ```bash
   astro dev start
   ```

---

## **Key Code Files**

### **1. Airflow DAG (`pipeline_dag.py`)**
Defines the workflow to ingest data, transform it using dbt, and analyze it with Snowpark.

```python
from airflow import DAG
from airflow.providers.snowflake.operators.snowflake import SnowflakeOperator
from airflow.providers.dbt.cloud.operators.dbt import DbtCloudRunJobOperator
from datetime import datetime

default_args = {
    'owner': 'airflow',
    'depends_on_past': False,
    'start_date': datetime(2023, 1, 1),
    'retries': 1
}

with DAG('data_pipeline', default_args=default_args, schedule_interval='@daily') as dag:
    load_raw_data = SnowflakeOperator(
        task_id='load_raw_data',
        sql="sql/load_data.sql",
        snowflake_conn_id='snowflake_default'
    )

    dbt_transform = DbtCloudRunJobOperator(
        task_id='dbt_transform',
        dbt_cloud_conn_id='dbt_default',
        job_id=<dbt_job_id>
    )

    load_raw_data >> dbt_transform
```

---

### **2. Snowflake SQL (`create_table.sql`)**
Creates the raw table for initial data ingestion.

```sql
CREATE TABLE my_table (
    id INT,
    name STRING,
    value FLOAT,
    created_at TIMESTAMP
);
```

### **3. Snowflake SQL (`load_data.sql`)**
Loads sample data into the raw table.

```sql
INSERT INTO my_table (id, name, value, created_at)
VALUES
    (1, 'Alice', 123.45, CURRENT_TIMESTAMP),
    (2, 'Bob', 678.90, CURRENT_TIMESTAMP);
```

---

### **4. dbt Model (`models/my_model.sql`)**
Transforms raw data into an enriched dataset.

```sql
SELECT
    id,
    name,
    value * 2 AS value_transformed,
    created_at
FROM
    {{ ref('my_table') }};
```

---

### **5. Snowpark Python Script (`analysis.py`)**
Performs advanced analytics within Snowflake using Python.

```python
from snowflake.snowpark import Session
from snowflake.snowpark.functions import col

# Create a Snowflake session
connection_params = {
    "account": "<account>",
    "user": "<user>",
    "password": "<password>",
    "role": "<role>",
    "warehouse": "<warehouse>",
    "database": "<database>",
    "schema": "<schema>"
}

session = Session.builder.configs(connection_params).create()

# Example: Aggregation in Snowpark
df = session.table("my_table")
aggregated_df = df.group_by("name").agg({"value": "avg"})
aggregated_df.show()
```

---

## **Execution Workflow**
1. **Start the Environment**:
   ```bash
   astro dev start
   ```

2. **Ingest Data**:
   - The Airflow task `load_raw_data` loads raw data into Snowflake.

3. **Transform Data**:
   - The Airflow task `dbt_transform` triggers dbt to transform raw data.

4. **Analyze Data**:
   - Snowpark scripts (e.g., `analysis.py`) execute advanced analytics.

5. **Monitor Progress**:
   - Use the Airflow UI to monitor tasks and identify any errors.

---

## **Repository Structure**

```plaintext
project/
│
├── dags/                       # Airflow DAGs
│   └── pipeline_dag.py         # Main DAG
│
├── dbt_project/                # dbt Project
│   ├── dbt_project.yml         # dbt Configuration
│   ├── profiles.yml            # Snowflake Connection
│   ├── models/                 # SQL Models
│   │   └── my_model.sql        # Transformations
│   └── tests/                  # Data Tests
│
├── snowflake/                  # Snowflake SQL Scripts
│   ├── create_table.sql        # Raw Table Creation
│   ├── load_data.sql           # Initial Data Load
│
├── snowpark/                   # Snowpark Analysis Scripts
│   └── analysis.py             # Python-based Advanced Analysis
│
├── docker-compose.yaml         # Docker Configuration for Airflow
├── requirements.txt            # Python Dependencies
├── README.md                   # Project Documentation
└── config.yaml                 # Astronomer CLI Configuration
```
