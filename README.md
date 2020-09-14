<h1 align="center"> Introduction and demo of GCP Composer </h1> 

<br>
<p align="center">
  <a href="https://gitpoint.co/">
    <img alt="" width="450">
  </a>
</p>
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/composer_logo.png"></p>

## Table of Contents

- [Composer Introduction](#composer_introduction)
- [Composer Flow](#composer_flow)
- [Differences between Dataflow and Composer](#differences)
- [Overview_Composer_Operations](#overview_composer_operations)
- [Demo](#demo)


<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Composer_Introduction

Cloud composer is workflow management system that you create, schedule, and monitor data pipelines that utilize the cloud and data centers. Composer is built on a service called Airflow which uses certain workflow configurations.
For more descrition see the [google documentation](https://cloud.google.com/composer/docs/concepts/overview). 

<br>

## Composer_Flow
<p align="center"><img width=80% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/composer_flow.png"></p>

## Differences
<p align="center"><img width=80% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/differences.png"></p>

## Overview_Composer_Operations

<details>
  <summary>1. Creating a Directed Acyclic Graph (DAG) using Python </summary>

Python_bash.py

Import statements
```
import datetime
from airflow import models
from airflow.operators import bash_operator
from airflow.operators import python_operator
```
Setting a variable for yesterday so we can be sure our DAG runs when we upload the Python file 
```
yesterday = datetime.datetime.combine(
    datetime.datetime.today() - datetime.timedelta(1),
    datetime.datetime.min.time())
default_dag_args = {'start_date': yesterday}
```
Setting the interval at 1 day to run the DAG
```
with models.DAG(
        'running_python_and_bash_operator',
        schedule_interval=datetime.timedelta(days=1),
        default_args=default_dag_args) as dag:
```
Any operators like the following will be added to the DAG object
```
# Python function
hello_world_greeting = python_operator.PythonOperator(
    task_id='python_1',
    python_callable=hello_world)

# Python function
sales_greeting = python_operator.PythonOperator(
    task_id='python_2',
    python_callable=greeting)

# Initiation of the bash operator
bash_greeting = bash_operator.BashOperator(
    task_id='bye_bash',
    bash_command='echo Goodbye! Hope to see you soon.')
```
Task order
```
hello_world_greeting >> sales_greeting >> bash_greeting
```

2. Upload the python script to the preformed bucket
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/upload_script.png"></p>

3. Open the Airflow console through the GCP Composer view
4. Airflow overview and detail screens
Note : Airflow has a slider button on the home page that indicates whether the DAG is live or dead. When using a test DAG to avoid GCP charges it is advisable to have the trigger off by turning the button to off status.

<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/airflow_screen_1st.png"></p>
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/airflow_screen_2nd.png"></p>


</details>


Configuring Trigger rules
Bash Operator
Python Operator
Branch Python Operator
Dummy Operator

