# heroku-airflow

[![Deploy](https://www.herokucdn.com/deploy/button.svg)](https://heroku.com/deploy)

Deploy [Airflow](https://airflow.apache.org/) to [Heroku](https://www.heroku.com/).

## Stack

- [Celery](https://docs.celeryq.dev/)
- [Mailgun](https://elements.heroku.com/addons/mailgun)
- [Postgres](https://elements.heroku.com/addons/heroku-postgresql)
- [Redis](https://elements.heroku.com/addons/rediscloud)

## Install

Create a virtual environment:

```sh
python3 -m venv venv
```

Activate the venv:

```sh
source venv/bin/activate
```

Install the dependencies:

```sh
pip3 install -r requirements.txt
```

## Run

Run an all-in-one copy of Airflow locally:

```sh
airflow standalone
```

## Env

Ensure the config vars from [app.json](app.json) are set in the Heroku app:

```sh
AIRFLOW__CELERY__WORKER_CONCURRENCY=2
AIRFLOW__CORE__FERNET_KEY=
AIRFLOW_HOME=/app
AIRFLOW__WEBSERVER__BASE_URL=
AIRFLOW__WEBSERVER__SECRET_KEY=
```

Generate a `AIRFLOW__CORE__FERNET_KEY`:

```sh
python3 -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())"
```

Set `AIRFLOW__CORE__FERNET_KEY` in Heroku app:

```sh
heroku config:set AIRFLOW__CORE__FERNET_KEY=$(python3 -c "from cryptography.fernet import Fernet; print(Fernet.generate_key().decode())")
```

Generate a `AIRFLOW__WEBSERVER__SECRET_KEY`:

```sh
openssl rand -base64 32 # head -c 32 /dev/random | base64
```

Set `AIRFLOW__WEBSERVER__SECRET_KEY` in Heroku app:

```sh
heroku config:set AIRFLOW__WEBSERVER__SECRET_KEY=$(openssl rand -base64 32)
```

## Database

If you're seeing the error in the Heroku logs:

```
ERROR: You need to initialize the database. Please run `airflow db init`.
```

Then verify the Airflow config looks good before initializing the database in your Heroku app:

```sh
heroku run bash
airflow info
airflow db init
```

## User

Create a user in the Heroku app:

```sh
heroku run bash
airflow users create -e EMAIL -f FIRSTNAME -l LASTNAME [-p PASSWORD] -r ROLE [--use-random-password] -u USERNAME
```

Here's an example on how to create an admin user:

```sh
airflow users create \
  --username admin \
  --firstname FIRST_NAME \
  --lastname LAST_NAME \
  --role Admin \
  --email admin@example.org
```

## Resources

- [heroku/heroku-airflow](https://github.com/heroku/heroku-airflow)
- [slyapustin/airflow-on-heroku](https://github.com/slyapustin/airflow-on-heroku)
- [jsoyland/heroku_airflow](https://github.com/jsoyland/heroku_airflow)
- [Running Airflow on Heroku](https://medium.com/@damesavram/running-airflow-on-heroku-ed1d28f8013d)
