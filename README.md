## Goals of this repo :

<p>
1. Introduce GCP Composer which is a data job scheduler
<br>
2. Demo GCP Composer
</p>
<br>
<br>
<br>
<h1 align="center"> Google Cloud Composer </h1> 

<br>
<p align="center">
  <a href="https://gitpoint.co/">
    <img alt="" width="450">
  </a>
</p>
<p align="center"><img width=40% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/composer_logo.png"></p>

## Table of Contents

- [Composer Introduction](#composer_introduction)
- [Composer Flow](#composer_flow)
- [Differences between Dataflow and Composer](#differences)
- [Composer_Operations](#composer_operations)


<!-- END doctoc generated TOC please keep comment here to allow auto update -->

## Composer_Introduction

Cloud composer is workflow management system that you create, schedule, and monitor data pipelines that utilize the cloud and data centers. Composer is built on a service called Airflow which uses certain workflow configurations.
For more descrition see the [google documentation](https://cloud.google.com/composer/docs/concepts/overview). 

<br>

## Composer_Flow
<p align="center"><img width=80% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/composer_flow.png"></p>

## Differences
<p align="center"><img width=80% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/differences.png"></p>

# Composer_Operations
<br>
<h3 align="center"> Running a sample DAG </h3> 
<br>
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
</details>

<details>
<summary>2. Upload the python script to the preformed bucket </summary>
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/upload_script.png"></p>
</details>

<summary>3. Open the Airflow console through the GCP Composer view </summary>
<br>

<details>
<summary>4. Airflow overview and detail screens </summary>
<br>
Note : Airflow has a slider button on the home page that indicates whether the DAG is active or non-active. When using a test DAG to avoid GCP charges, it is advisable to have the trigger off by turning the button to off status.

<p align="center"><img width=100% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/airflow_screen_1st.png"></p>
<p align="center"><img width=100% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/airflow_screen_2nd.png"></p>
</details>
</details>
</details>

<br>
<br>
<h3 align="center"> Configuring Trigger rules</h3>

The following code sets the retry statement at 1 and the retry delay at 2min 
```
default_dag_args = {
    'start_date': yesterday,
    'retries': 1,
    'retry_delay': datetime.timedelta(minutes=2)}
```
Also when triggered, we forced a ValueError to see if we get the proper response from running the DAG. 
```
def hello_world():
    raise ValueError('Oops! something went wrong.')
    print('Hello World!')
    return 1
```

The DAG ran as expected and gave us an error.
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/failure_retry_1.png"></p>
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/failure_retry_2.png"></p>
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/failure_retry_3.png"></p>

A triggering rule can also be set with the bash operator
```
bash_greeting = bash_operator.BashOperator(
    task_id='bye_bash',
    bash_command='echo Goodbye! Hope to see you soon.',
    trigger_rule=trigger_rule.TriggerRule.ONE_FAILED)
```
</details>

<h3 align="center"> Dummy Operator</h3>

In python_dash_dummy.py, an additional dummy statement is added for branching.
Because most branching has a join operation, the dummy statement fills one of legs when branching. 
```
# Addiotions to the python code
def makeBranchChoice():
        x = random.randint(1, 5)
        if(x <= 2):
            return 'hello'
        else:
            return 'dummy'  
    run_this_first = dummy_operator.DummyOperator(
        task_id='run_this_first')
    branching = python_operator.BranchPythonOperator(
        task_id='branching',
        python_callable=makeBranchChoice)
    run_this_first >> branching
    sales_greeting = python_operator.PythonOperator(
        task_id='hello',
        python_callable=greeting)
    dummy_followed_python = dummy_operator.DummyOperator(
        task_id='follow_python')
    dummy = dummy_operator.DummyOperator(
        task_id='dummy')
    bash_greeting = bash_operator.BashOperator(
        task_id='bye_bash',
        bash_command='echo Goodbye! Hope to see you soon.',
        trigger_rule='one_success'
    )
```

The above code generated a path with a dummy operator. In the if-else condition, the x > 2 so it followed the dummy path. If a dummy path was not programmed the DAG would have failed.

<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/branching_python_1.png"></p>
<p align="center"><img width=60% src="https://github.com/hilsdsg3/Google_Cloud_Platform_Composer/blob/master/meta_data/branching_python_2.png"></p>


</details>












