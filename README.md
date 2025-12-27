# Apache Airflow Big Data Pipeline

A complete end-to-end Big Data pipeline orchestrated with Apache Airflow, demonstrating the flow from data ingestion through Data Lake, processing, to Data Lakehouse for analytics.

## Table of Contents

- [Overview](#overview)
- [Architecture](#architecture)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Installation](#installation)
- [Pipeline Workflow](#pipeline-workflow)
- [Monitoring & Logs](#monitoring--logs)
- [Troubleshooting](#troubleshooting)

## Overview

This project implements a complete Big Data pipeline that demonstrates:

- **Data Ingestion**: Simulating data collection from sources
- **Data Lake (RAW)**: Storage of raw, unprocessed data
- **Big Data Processing**: Data transformation and cleaning
- **Data Lakehouse (CURATED)**: Storage of refined, analysis-ready data
- **Analytics**: Final stage for BI and Machine Learning consumption

The pipeline is orchestrated using Apache Airflow, providing:
-  Task scheduling and dependency management
-  Error handling and retry mechanisms
-  Visual monitoring interface
-  Complete execution logs

## Architecture

### Pipeline Flow

```
Sources → Ingestion → Data Lake (RAW) → Processing → Lakehouse (CURATED) → Analytics/BI/AI
```

### Technical Components

- **Apache Airflow 2.8.1**: Workflow orchestration
- **PostgreSQL 13**: Airflow metadata storage
- **Python**: Data processing scripts
- **Docker & Docker Compose**: Containerization


<img width="923" height="73" alt="image" src="https://github.com/user-attachments/assets/eba4b712-d547-44f6-84ce-b1f215f77454" />


## Prerequisites

Before starting, ensure you have:

- Docker Engine 20.10+
- Docker Compose 2.0+
- 4GB RAM minimum
- 10GB free disk space

## Project Structure

```
airflow-bigdata-pipeline/
│
├── docker-compose.yml          # Docker services configuration
├── commands.txt                # Quick reference commands
├── dags/
│   └── bigdata_pipeline.py    # Main DAG definition
├── data/
│   ├── raw/                   # Data Lake (raw data)
│   ├── processed/             # Intermediate processing zone
│   └── curated/               # Data Lakehouse (curated data)
├── .gitignore
├── LICENSE
└── README.md
```

### Data Flow Zones

| Zone | Purpose | Data State |
|------|---------|------------|
| **raw/** | Data Lake | Unprocessed, original data |
| **processed/** | Intermediate | Cleaned and validated |
| **curated/** | Data Lakehouse | Analysis-ready, structured |

## Installation

### Step 1: Clone the Repository

```bash
git clone https://github.com/ynstf/airflow-bigdata-pipeline.git
cd airflow-bigdata-pipeline
```

### Step 2: Initialize Airflow Database

**Execute only once on first setup:**

```bash
docker-compose run airflow-webserver airflow db init
```

<img width="1246" height="704" alt="image" src="https://github.com/user-attachments/assets/8158501f-45ac-4e5b-9fff-30f44f835c64" />
<img width="1345" height="314" alt="image" src="https://github.com/user-attachments/assets/8fa2dbbd-30fd-46b9-80d1-c4a38351c4f6" />



### Step 3: Create Admin User

**Execute only once on first setup:**

```bash
docker-compose run airflow-webserver airflow users create \
  --username airflow \
  --password airflow \
  --firstname Airflow \
  --lastname Admin \
  --role Admin \
  --email admin@airflow.local
```


<img width="1344" height="671" alt="image" src="https://github.com/user-attachments/assets/1deaaa2c-34e5-4fd2-9d63-cc03f71cc5ce" />


### Step 4: Start Services

```bash
docker-compose up -d
```

### Step 5: Verify Services

Check that all containers are running:

```bash
docker-compose ps
```

Expected output:
```
NAME                          STATUS
airflow-webserver             Up
airflow-scheduler             Up
postgres                      Up
```


<img width="949" height="245" alt="image" src="https://github.com/user-attachments/assets/344f1710-b30d-488d-be81-01d40be4d947" />


##  Pipeline Workflow

The DAG (`bigdata_pipeline_complete`) consists of 5 sequential tasks:

### Ingest (`ingest`)
- **Purpose**: Simulate data ingestion from sources
- **Action**: Creates `sales.csv` in the RAW zone
- **Output**: `data/raw/sales.csv`

### Validate (`validate`)
- **Purpose**: Data quality checks
- **Action**: Verifies file existence
- **Behavior**: Raises error if data missing

### Transform (`transform`)
- **Purpose**: Big Data processing (simulated)
- **Action**: Cleans and structures data
- **Output**: `data/processed/sales_clean.csv`

### Load to Lakehouse (`load_lakehouse`)
- **Purpose**: Populate Data Lakehouse
- **Action**: Moves curated data to final zone
- **Output**: `data/curated/sales_curated.csv`

### Analytics (`analytics`)
- **Purpose**: Ready for BI/ML consumption
- **Action**: Marks data as analysis-ready

### Task Dependencies

```
ingest >> validate >> transform >> load_lakehouse >> analytics
```

<img width="1327" height="619" alt="image" src="https://github.com/user-attachments/assets/b79714ca-c490-47d0-b2d2-0f21f0f1d0f2" />


## Usage

### Access Airflow Web Interface

1. Open your browser and navigate to:
   ```
   http://localhost:8080
   ```

2. Login with credentials:
   - **Username**: `airflow`
   - **Password**: `airflow`


<img width="1308" height="424" alt="image" src="https://github.com/user-attachments/assets/e7fa6267-cc87-4cb4-86db-28ed8b38802f" />


### Activate the DAG

1. Locate `bigdata_pipeline_complete` in the DAGs list
2. Toggle the switch to **ON** (left side of DAG name)

<img width="1337" height="419" alt="image" src="https://github.com/user-attachments/assets/49a6a8b5-e0f0-4dd0-a4b4-7aff20cdc133" />


### Trigger Manual Execution

1. Click on the DAG name: `bigdata_pipeline_complete`
2. Click the **▶️ Trigger DAG** button (top right)
3. Confirm the execution


<img width="1326" height="152" alt="image" src="https://github.com/user-attachments/assets/c9e283c7-d564-4874-9fcc-7018d93ea8d3" />


### Monitor Execution

#### Graph View

Navigate to the **Graph** tab to see:
- Task execution order
- Current status of each task
- Dependencies visualization

**Status Colors:**
- 🟢 **Green**: Success
- 🔴 **Red**: Failed
- 🔵 **Blue**: Running
- ⚫ **Gray**: Not executed yet


#### Grid View

The Grid view shows:
- Historical run timeline
- Quick status overview
- Run duration

<img width="1321" height="532" alt="image" src="https://github.com/user-attachments/assets/65067b8e-773c-4a00-80d5-0180ee2f499f" />


### Verify Pipeline Results

After successful execution, check that files were created:

```bash
ls -la data/raw/
ls -la data/processed/
ls -la data/curated/
```

Expected files:
```
data/raw/sales.csv
data/processed/sales_clean.csv
data/curated/sales_curated.csv
```

## Monitoring & Logs

### View Task Logs

1. Click on any task in the Graph view
2. Select **Log** from the popup menu
3. Review execution details

<img width="1306" height="612" alt="image" src="https://github.com/user-attachments/assets/56e5d62c-e905-4c45-9b8f-79a6d3acb3b2" />


### Understanding Logs

Logs provide:
- Task execution timestamps
- Python function outputs
- Error messages (if any)
- Resource usage information

### Common Log Entries

**Successful Task:**
```
[2025-12-27] Task succeeded in 1s
```

**Failed Task:**
```
[2025-12-27] ERROR - Task failed with exception
```

## Troubleshooting

### Issue: DAG Not Appearing

**Solution:**
```bash
# Restart scheduler
docker-compose restart airflow-scheduler

# Check DAG for syntax errors
docker-compose exec airflow-webserver python /opt/airflow/dags/bigdata_pipeline.py
```

### Issue: Task Failed

**Steps:**
1. Click on the failed task (red)
2. View logs to identify error
3. Click **Clear** to reset task state
4. Trigger DAG again

<img width="1325" height="570" alt="image" src="https://github.com/user-attachments/assets/e26f180a-acfc-423a-a9d5-4f705f76d9ab" />


### Issue: Connection Refused to Port 8080

**Solution:**
```bash
# Check if port is in use
sudo lsof -i :8080

# Stop all containers and restart
docker-compose down
docker-compose up -d
```

### Issue: Database Connection Error

**Solution:**
```bash
# Reset database
docker-compose down -v
docker-compose run airflow-webserver airflow db init
# Recreate admin user (see Installation Step 3)
docker-compose up -d
```

## Advanced Configuration

### Schedule Interval

Modify the schedule in `dags/bigdata_pipeline.py`:

```python
schedule_interval="@daily",  # Runs once per day
# Other options:
# "@hourly"  - Every hour
# "@weekly"  - Every week
# "0 9 * * *"  - Every day at 9 AM (cron syntax)
```

### Adding More Data Sources

Extend the `ingest_data()` function:

```python
def ingest_data():
    os.makedirs(RAW, exist_ok=True)
    
    # Add more data sources
    with open(f"{RAW}/customers.csv", "w") as f:
        f.write("id,name\n1,Alice\n2,Bob")
    
    with open(f"{RAW}/products.csv", "w") as f:
        f.write("id,product\n1,Laptop\n2,Phone")
```

## Production Considerations

When moving to production:

1. **Use a robust executor**: Consider CeleryExecutor or KubernetesExecutor
2. **External database**: Use managed PostgreSQL instead of Docker container
3. **Secrets management**: Use Airflow Connections and Variables
4. **Monitoring**: Integrate with Prometheus, Grafana, or cloud monitoring
5. **Scaling**: Add worker nodes for parallel task execution
6. **Security**: Enable RBAC, LDAP authentication, SSL/TLS

---
