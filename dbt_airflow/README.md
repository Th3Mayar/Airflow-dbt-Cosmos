# **Data Engineering Pipeline with Airflow, Snowflake, dbt, and Snowpark**

## **Introduction**
This project implements a modern and automated data pipeline using leading tools in the data engineering ecosystem. The primary goal is to orchestrate, transform, and analyze data efficiently, leveraging the scalability of Snowflake and the flexibility of Apache Airflow and dbt.

---

## **Objectives**
1. Automate the data workflow from ingestion to transformation and analysis.
2. Implement reproducible and versioned transformations with dbt.
3. Execute advanced analyses using Snowpark in Snowflake.
4. Orchestrate and schedule all tasks with Apache Airflow.
5. Ensure a modular design for reusability and scalability.

---

## **Architecture**
The project follows a modular and flexible architecture organized into the following layers:

1. **Data Ingestion**:
   - Load raw data from files or external sources into Snowflake.

2. **Transformation**:
   - dbt transforms data in Snowflake, producing cleaned and enriched tables.

3. **Advanced Analysis**:
   - Snowpark executes Python scripts for advanced analysis directly in Snowflake.

4. **Orchestration**:
   - Airflow coordinates and schedules all tasks in the pipeline.

---

## **Technologies Used**

### **1. Apache Airflow**
- Workflow orchestration tool.
- DAGs are used to coordinate tasks like data loading, dbt transformations, and Snowpark analyses.

### **2. Snowflake**
- Cloud-based data warehouse to store and process data.
- Optimized for SQL queries and handling large datasets.

### **3. Snowpark**
- Snowflake extension for running advanced logic in Python directly in the database.

### **4. dbt (Data Build Tool)**
- Simplifies data transformations using SQL.
- Handles transformation versioning, lineage, and testing.

### **5. Cosmos**
- Extends Airflow to manage modular workflows.

---

## **Pipeline Workflow**

1. **Data Ingestion**:
   - Airflow extracts data from external sources (CSV, JSON, databases, APIs) and loads it into a raw table in Snowflake.

2. **Transformation with dbt**:
   - dbt executes SQL models to:
     - Clean and enrich the data.
     - Create calculated metrics and aggregated tables.
     - Document and test transformations.

3. **Advanced Analysis with Snowpark**:
   - Python scripts run complex logic directly in Snowflake, such as:
     - Predictive models.
     - Custom algorithms.

4. **Delivering Results**:
   - Transformed and analyzed data is stored in final tables in Snowflake, ready to be consumed by BI tools like Tableau or Power BI.

5. **Automation**:
   - Airflow schedules and monitors all pipeline tasks.

---

## **Project Structure**

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

---

## **Setup**

### **1. Snowflake Configuration**
- Create a warehouse, database, and schema in Snowflake.
- Load initial data into a raw table using `create_table.sql` and `load_data.sql`.

### **2. dbt Configuration**
- Configure `profiles.yml` to connect to Snowflake.
- Write SQL models in the `models/` directory to transform the data.
- Run and test transformations with:

  ```bash
  dbt run
  dbt test
  ```

### **3. Airflow Configuration**
- Define the main DAG in `pipeline_dag.py`.
- Configure connections to Snowflake and dbt in the Airflow interface.
- Start Airflow using Astronomer CLI or Docker Compose:

  ```bash
  astro dev start
  ```

### **4. Snowpark Configuration**
- Write analysis scripts in Python and execute them directly in Snowflake.

---

## **Pipeline Execution**

1. **Start the Environment**:
   ```bash
   astro dev start
   ```

2. **Load Data into Snowflake**:
   - Execute the initial DAG tasks to load data into the raw table.

3. **Run Transformations with dbt**:
   - The DAG triggers dbt SQL models to transform the data.

4. **Run Analysis with Snowpark**:
   - Python scripts are executed as part of the DAG to perform advanced analyses.

5. **Monitor the Pipeline**:
   - Use the Airflow interface to track task statuses and debug issues.

---

## **Testing**

1. **Transformation Testing with dbt**:
   - Define tests in the `tests/` directory and run them:

     ```bash
     dbt test
     ```

2. **Result Validation**:
   - Verify that the transformed tables in Snowflake meet expected results. 