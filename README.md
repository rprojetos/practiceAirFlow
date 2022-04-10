<img src='img/airflow.png' width= '100%'>

## Verification Pip Instalation:
Verify if pip is instaled
> sudo apt install python3-pip

## Enviroment variable to the Airflow

Create the enviroment variable: AIRFLOW_HOME=~/airflow
* Reference to the instalation local of the Airflow
Insert in the .bashrc file in the end line.

> nano ~/.bashrc

and insert, in the end line:

`
export AIRFLOW_HOME=~/airflow

^O --> Confirmate modification
and
^X --> To exit
`

###### Testing enviroment variable
- > Open new bash terminal, and write: 
> echo $AIRFLOW_HOME

## Airflow Instalation

pip3 install apache-airflow

airflow --version

## Database initialazing
That case initialize sqlite only, for configuration. 
> airflow db init

## Airflow configuration in the directory $AIRFLOW_HOME
echo $AIRFLOW_HOME

![](img/2022-04-10-10-28-34.png)

## Go to ~/airflow directory.

cd ~/airflow

This is the configuration file of the Airflow.
> nano airflow.cfg

## Create Airflow User

examples:
To create an user with "Admin" role and username equals to "admin", run:

> airflow users create \\
    --username admin \\
    --firstname FIRST_NAME \\
    --lastname LAST_NAME \\
    --role Admin \\
    --email admin@example.org

![](img/2022-04-10-10-50-30.png)

## Initialize the Airflow web interface and Airflow Scheduler

> airflow webserver --port 8080

![](img/2022-04-10-11-07-20.png)

Access the Web Interface in the browser:
> localhost:8080

![](img/2022-04-10-11-03-06.png)

![](img/2022-04-10-11-10-13.png)


## Initialize scheduler

> airflow scheduler

![](img/2022-04-10-11-15-02.png)

## Configuration the Airflow to production 

1. ##### Edit the configuration file:
> nano ~/airflow/airflow.cfg

2. ##### Go to conection string of the database

The SqlAlchemy connection string to the metadata database.
SqlAlchemy supports many different database engines.
More information here:
http://airflow.apache.org/docs/apache-airflow/stable/howto/set-up-database.html#database-uri

> sql_alchemy_conn = sqlite:////home/rprojetos/airflow/airflow.db

Comment the above line and add next new line 

> sql_alchemy_conn = postgresql+psycopg2://airflow:airflow@localhost/airflow

3. ##### In the case using postgresql, alter the line:

The executor class that airflow should use. Choices include
``SequentialExecutor``, ``LocalExecutor``, ``CeleryExecutor``, ``DaskExecutor``,
``KubernetesExecutor``, ``CeleryKubernetesExecutor`` or the
full import path to the class when using a custom executor.


> executor = SequentialExecutor

.. alter to:

> executor = LocalExecutor

# Install database Postgresql

##### Instaling the Postgresql
> sudo apt install postgresql postgresql-contrib

##### Configuring the Postgresql

Listing the instalation directory of the postgresql
> ls /etc/postgresql/12/main

![](img/2022-04-10-13-44-22.png)

Check if Postgresql have IPV4 local connections

> sudo nano /etc/postgresql/12/main/pg_hba.conf

![](img/2022-04-10-13-48-29.png)

Configuration the postgresql to listen only localhost

![](img/2022-04-10-14-06-20.png)

^O Enter ^X

##### Restart the Postgresql

> service postgresql restart

# Acess the Postgresql with user created
Access to the Interactive Terminal of the Postgresql
> sudo -u postgres psql

![](img/2022-04-10-14-19-26.png)

Creating user and password:
> postgres=# CREATE USER airflow PASSWORD 'airflow';

Creating database
> postgres=# CREATE DATABASE airflow;

`
User, password and database name settings must be the same as those configured in ~/airflow/airflow.cfg
`

Releasing access permission:

> postgres=# GRANT ALL PRIVILEGES ON ALL TABLES IN SCHEMA public TO airflow;

> postgres=# GRANT ALL PRIVILEGES ON DATABASE airflow TO airflow;

Listing users:
> postgres=# \du

![](img/2022-04-10-15-20-52.png)


Listing all server banks along with their owners and encodings:
> postgres=# \l

![](img/2022-04-10-15-36-03.png)

# Initializing and configuring the new metastore DB with the PostgreSQL

Connection package of the DB
> pip3 install psycopg2-binary 



This command read the configuration file(~/airflow/airflow.cfg) of the DB and Initialize DB with this configuration.

> airflow db init

![](img/2022-04-10-15-49-23.png)

Now the Airflow be using the PostgreSQL!

As we change from sqlite database to postgresql, we have to create airflow user again:

> airflow users create \\
    --username admin \\
    --firstname FIRST_NAME \\
    --lastname LAST_NAME \\
    --role Admin \\
    --email admin@example.org

![](img/2022-04-10-10-50-30.png)

So, let's run the web server
> airflow webserver --port 8080

And now, let's run the scheduler
> airflow scheduler 

