# Introduction to Apache Airflow: Automating and Managing Workflows

## Introduction  
Apache Airflow is an open-source platform designed to author, schedule, and monitor workflows programmatically. In today’s data-driven world, automating complex processes—such as data pipelines, machine learning model training, and ETL (Extract, Transform, Load) tasks—is critical for efficiency and scalability. Airflow allows teams to define workflows as code, making them reusable, dynamic, and easily maintainable. Whether orchestrating daily data ingestion or coordinating multi-team workflows, Airflow provides the tools to ensure tasks run in the correct order, at the right time, and with proper monitoring.

---

## Learning Objectives  
1. **Understand** the core components of Apache Airflow, including DAGs, Operators, and Tasks.  
2. **Design** a basic workflow using Airflow’s Python-based framework.  
3. **Monitor** and troubleshoot workflows using Airflow’s UI and logging features.  

---

## What is a Workflow?  
A workflow is a sequence of tasks executed in a specific order to achieve a goal. In data engineering, workflows might include steps like fetching data, cleaning it, analyzing it, and generating reports. Manually managing these processes is error-prone and inefficient. Apache Airflow solves this by allowing you to define workflows as code, automating their execution and monitoring.  

---

## Core Components of Apache Airflow  

### 1. Directed Acyclic Graph (DAG)  
- **Core Idea**: A DAG defines the workflow structure, representing tasks and their dependencies. It ensures tasks run in the correct order without cycles.  
- **Key Features**:  
  - Written in Python, making it dynamic and flexible.  
  - Scheduled using cron-like syntax or time intervals.  
  - Enables task reuse and modularity.  

# Directed Acyclic Graph (DAG) in Apache Airflow

A **Directed Acyclic Graph (DAG)** is the backbone of Apache Airflow's workflow management system. It represents a collection of all the tasks you want to run, organized to reflect their relationships and dependencies.

## Core Concept

- **Directed**: The workflow has a specific flow from one task to another.
- **Acyclic**: No cycles are allowed; a task cannot be dependent on itself either directly or indirectly.
- **Graph**: Composed of nodes (tasks) and edges (dependencies), forming a structure that defines execution order.

This structure ensures tasks execute in a valid, logical sequence, preventing circular dependencies and allowing for deterministic workflows.

## Key Features

- **Python-Based**: DAGs are defined in standard Python files, allowing dynamic and programmatic structures to be used.
- **Flexible Scheduling**: DAGs can be scheduled using cron expressions or defined time intervals, making them suitable for various use cases.
- **Task Modularity and Reusability**: Tasks and operators can be reused across different DAGs, supporting DRY (Don't Repeat Yourself) principles.
- **Clear Dependency Management**: Dependencies between tasks are explicitly defined, ensuring predictable execution flows.
- **Retry and Alert Mechanisms**: DAGs support configurable retry strategies and failure notifications to enhance reliability.
- **Backfilling and Catch-up**: Missed runs can be automatically executed when a DAG is re-enabled or updated, ensuring data consistency over time.

## DAG Characteristics

- **Declarative Structure**: You define what should be done and in what order, rather than how to do it step by step.
- **Dynamic Creation**: DAGs can be created dynamically using loops or external configuration files, enabling scalable and templated workflows.
- **Configurability**: Settings such as owner, retry logic, start date, and timeout behavior can be easily customized per DAG or task.
- **Visibility and Monitoring**: The Airflow UI provides a visual representation of DAGs, task statuses, execution times, and logs, aiding in debugging and management.

## Best Practices

- **Name DAGs and Tasks Clearly**: Use descriptive names to make workflows self-explanatory.
- **Keep DAGs Lightweight**: Avoid heavy computations or API calls in the DAG definition file itself.
- **Avoid Cyclic Dependencies**: Ensure the task dependency graph remains acyclic.
- **Separate Logic from Configuration**: Encapsulate business logic in external scripts or modules to keep DAGs clean and readable.
- **Limit DAG File Complexity**: Keep each DAG focused and manageable; if it gets too large, consider breaking it into multiple DAGs.

---






# Scheduling with Cron in Apache Airflow

## Overview

Apache Airflow uses cron expressions to schedule DAG runs. This allows workflows to be triggered automatically at specified intervals, providing flexibility and precision in timing.

## Cron Expression Format

A cron expression has five fields, each separated by a space:

│ │ │ │ │
│ │ │ │ └── Day of the week (0 - 6) (Sunday=0)
│ │ │ └──── Month (1 - 12)
│ │ └────── Day of the month (1 - 31)
│ └──────── Hour (0 - 23)
└────────── Minute (0 - 59)





## Common Examples

- `@hourly` – Runs once every hour
- `@daily` – Runs once a day at midnight (00:00)
- `0 6 * * *` – Runs daily at 6:00 AM
- `30 2 * * 1-5` – Runs at 2:30 AM, Monday through Friday
- `0 0 1 * *` – Runs on the 1st of every month at midnight

## Preset Options

Airflow supports special preset strings as shortcuts:

- `@once` – Run once and never again
- `@hourly` – Every hour
- `@daily` or `@midnight` – Once a day at 00:00
- `@weekly` – Once a week at midnight on Sunday
- `@monthly` – Once a month on the first day at midnight
- `@yearly` or `@annually` – Once a year at midnight on January 1st

## Best Practices

- Use `catchup=False` in your DAG definition if you do not want Airflow to run all missed intervals.
- Ensure system time (or Airflow's time zone settings) aligns with your intended schedule.
- Prefer preset cron strings (e.g., `@daily`) for simplicity unless you need fine-grained control.

---


## Operators

**Core Idea**: Operators determine what a task does. Airflow provides pre-built operators for common actions (e.g., `PythonOperator`, `BashOperator`, `EmailOperator`).

# Operators in Apache Airflow

## Core Idea

**Operators** define the specific action that a task will perform in a DAG. They are the building blocks of task logic and encapsulate the work that needs to be done, such as running a script, transferring data, or executing a SQL command.

Airflow provides a rich set of **pre-built operators** to support common use cases. These operators abstract away low-level code, allowing developers to define tasks using high-level interfaces.

## Common Operator Types

- **PythonOperator**: Executes a Python function.
- **BashOperator**: Runs a Bash command or script.
- **EmailOperator**: Sends email alerts or notifications.
- **DummyOperator**: Used as a placeholder or for logical structuring in the DAG.
- **BranchPythonOperator**: Enables branching logic based on conditional outcomes.
- **Sensor Operators**: Wait for a condition to be met before continuing, e.g., FileSensor, S3KeySensor.
- **SQL Operators**: Run SQL queries against databases (e.g., PostgresOperator, MySqlOperator).
- **DockerOperator**: Runs a task inside a Docker container.
- **KubernetesPodOperator**: Launches tasks in Kubernetes pods.

## Operator Customization

Operators are highly configurable. You can set:
- Task retries and retry delay
- Timeout and SLA (Service Level Agreement)
- Email or callback alerts on failure
- Template fields for dynamic values using Jinja

## Reusability and Modularity

Operators promote code reuse. Instead of writing the same logic in multiple places, you can define a custom operator once and use it across many DAGs. This keeps workflows maintainable and modular.

## Extending Operators

Airflow also supports the creation of **custom operators** by subclassing the base operator classes. This is useful when your task logic is not covered by built-in operators or requires integration with specific systems.

## Best Practices

- Choose the simplest operator that fits your use case.
- Avoid placing business logic directly in the operator definition; delegate to external functions or scripts.
- Use sensors instead of manual polling logic.
- Group related tasks logically, even using different operators, for better DAG readability.

---

Operators allow you to encapsulate task behavior cleanly and consistently, making Airflow workflows powerful and easy to manage.

```

## Tasks

**Core Idea**: Tasks are individual units of work within a DAG. Each task is an instance of an operator.

**Key Features**:
- Tasks can depend on other tasks using `>>` or `set_downstream()`
- Retries and error handling are configurable per task

**Example Task Dependencies**:
```python
extract >> process_data  # Ensures 'process_data' runs after 'extract'
```

## Airflow Architecture: How It Works

Airflow's modular architecture includes:
- **Scheduler**: Triggers workflows and submits tasks to the executor
- **Executor**: Runs tasks (locally or on distributed systems like Kubernetes)
- **Web Server**: UI for monitoring DAGs, inspecting logs, and managing tasks
- **Metadata Database**: Stores DAG definitions, task states, and execution history






<img width="348" alt="Capture" src="https://github.com/user-attachments/assets/738c44c6-5c26-467d-9b0f-a8304c7d92be" />











## Use Cases for Apache Airflow
1. **Data Pipelines**: Automate ETL processes across databases and cloud services (e.g., AWS S3, Snowflake)
2. **Machine Learning**: Schedule model training, data validation, and deployment tasks
3. **DevOps Automation**: Run infrastructure checks, backups, and deployment scripts
4. **Business Reports**: Generate daily/weekly analytics reports

## Building a Workflow: 5-Step Guide

| Step | Description | Example |
|------|-------------|---------|
| 1. Define Goals | Identify tasks, dependencies, and scheduling needs | "Load daily sales data by 5 AM" |
| 2. Write DAG File | Structure tasks in Python | Store in `dags/` folder |
| 3. Test DAG | Validate tasks | `airflow tasks test my_dag extract_task 2024-01-01` |
| 4. Deploy & Monitor | Use Airflow UI | Check Graph View for progress |
| 5. Handle Failures | Configure error handling | Retry 3x + email alerts |

## Key Metrics to Monitor
- ✅ DAG Execution Time
- ✅ Task Success Rate
- ⚠️ Scheduler Latency
- 📈 Resource Utilization (CPU/Memory)

## Lesson Summary
1. **Core Components**: DAGs + Operators + Tasks
2. **Flexibility**: Python-based workflow design
3. **Scalability**: Kubernetes support
4. **Monitoring**: Real-time UI insights

## Challenge: Create API Data Pipeline DAG

# Challenge: Create API Data Pipeline DAG in Apache Airflow

## Objective

Design a DAG that periodically fetches data from an external API, processes it, and stores it in a destination such as a database, cloud storage, or a data warehouse.

## Pipeline Stages

1. **Extract**
   - Triggered at scheduled intervals (e.g., hourly, daily).
   - Calls an external REST API to retrieve data.
   - Handles authentication, pagination, and rate limits if needed.

2. **Transform**
   - Parses and cleans raw API response (e.g., JSON to structured format).
   - Applies any necessary data transformation or enrichment (e.g., converting timestamps, normalizing fields).

3. **Load**
   - Writes the processed data to the target system, such as:
     - PostgreSQL/MySQL database
     - Amazon S3 or Google Cloud Storage
     - BigQuery, Snowflake, or Redshift

4. **Validation**
   - Performs basic checks to ensure data was loaded successfully.
   - Optionally sends notifications (e.g., Slack or Email) upon success or failure.

## Operators to Use

- **HTTP Sensor or Custom Sensor**: Waits for API availability (optional).
- **PythonOperator**: Used for data extraction, transformation, and loading.
- **BranchPythonOperator**: Implements conditional logic (e.g., skip if API has no new data).
- **DummyOperator**: Marks pipeline boundaries (start, end).
- **EmailOperator or Slack Hook**: Sends alerts for pipeline success or failure.

## Key Considerations

- **Error Handling**: Implement retries and alerts for failed API calls or transformations.
- **Idempotency**: Ensure the pipeline doesn't duplicate data if re-run.
- **Logging and Monitoring**: Add detailed logging for troubleshooting and auditing.
- **Templating and Parameters**: Use Airflow macros to pass dates or runtime variables dynamically into API requests.
- **Environment Variables**: Store API keys and secrets securely using Airflow’s Variables or Connections.

## Scheduling Strategy

- Choose an interval aligned with the API’s update frequency.
- Use `catchup=False` if you don’t want past runs to backfill automatically.
- Optionally implement a DAG-level SLA to monitor pipeline performance.

## Extension Ideas

- Chain multiple API calls together (e.g., fetch metadata and then detailed records).
- Parallelize requests to speed up data collection.
- Archive raw API responses for debugging or audit purposes.
- Use TaskFlow API for cleaner, function-based DAGs.

# Resources

## Official Documentation
- Apache Airflow. (n.d.). Documentation. [online] Available at: https://airflow.apache.org/docs/.

## Video Tutorials
- coder2j (2022). Airflow Tutorial for Beginners - Full Course in 2 Hours 2022. [online] YouTube. Available at: https://www.youtube.com/watch?v=K9AnJ9_ZAXE.

## Medium Articles
- Susanto, G.K. (2021) Data pipeline using Apache airflow to import data from public API, jakartasmartcity. Available at: https://medium.com/jakartasmartcity/data-pipeline-using-apache-airflow-to-import-data-from-public-api-7ff719118ac8.

- Obregon, A. (2024) Build data pipelines with Apache Airflow, Medium. Available at: https://medium.com/@AlexanderObregon/implementing-data-pipelines-with-apache-airflow-in-python-69761ed65bdb.

- Rehmanabdul (2024). How to Automate Data Pipelines in Apache Airflow | by Rehmanabdul | Medium | CodeX. [online] Medium. Available at: https://medium.com/codex/how-to-automate-data-pipelines-in-apache-airflow-9fc90291371a.

- Radečić, D. (2022). Apache Airflow for Data Science — How to Work with REST APIs. [online] Medium. Available at: https://medium.com/data-science/apache-airflow-for-data-science-how-to-work-with-rest-apis-8f4e20bee7d.
