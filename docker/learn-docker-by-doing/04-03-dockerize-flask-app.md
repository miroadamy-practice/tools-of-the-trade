# Dockerize a Flask Application

## Introduction

Migrating static content into containers was a great way to learn the basics of Docker, but real-world uses are usually dynamic, including full applications.

This lab will show you the process to dockerize a Flask application. Flask is a lightweight Python WSGI micro web framework, however, you won't need to know any Python to complete this lab.

## Solution

Log in to the server using the credentials provided:

`ssh cloud_user@<PUBLIC_IP_ADDRESS>`

### Create the Build Files

1. Change to the `notes` directory:
    
    `cd notes`
    
2. List the files in the directory:
    
    `ls -la`
    
3. Inspect the `config.py` file:
    
    `cat config.py`
    
4. Crate the `.dockerignore` file:
    
    `vim .dockerignore`
    
5. In the file, paste the following:
    
    `.dockerignore
    Dockerfile
    .gitignore
    Pipfile.lock
    migrations/`
    
6. Save the file:
    
    `ESC
    :wq`
    
7. Create the `Dockerfile`:
    
    `vim Dockerfile`
    
8. In the file, paste the following:
    
    `FROM python:3
    ENV PYBASE /pybase
    ENV PYTHONUSERBASE $PYBASE
    ENV PATH $PYBASE/bin:$PATH
    RUN pip install pipenv
    
    WORKDIR /tmp
    COPY Pipfile .
    RUN pipenv lock
    RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile
    
    COPY . /app/notes
    WORKDIR /app/notes
    EXPOSE 80
    CMD ["flask", "run", "--port=80", "--host=0.0.0.0"]`
    

### Build and Setup Environment

1. Build the `notesapp` image:
    
    `docker build -t notesapp:0.1 .`
    
2. Check the status of the image:
    
    `docker images`
    
3. List the containers:
    
    `docker ps -a`
    
4. List the docker networks:
    
    `docker network ls`
    
5. Run a container using the `notesapp` image and mount the `migrations` directory:
    
    `docker run --rm -it --network notes -v /home/cloud_user/notes/migrations:/app/notes/migrations notesapp:0.1 bash`
    
6. Once connected to the container, enable `SQLAlchemy`:
    
    `flask db init`
    
7. Check the `migrations` folder:
    
    `ls -l migrations`
    
8. Create the files needed to configure the database:
    
    `flask db migrate`
    
9. Apply the files:
    
    `flask db upgrade`
    

### Run, Evaluate, and Upgrade

1. Run a container using the `notesapp:0.1` image:
    
    `docker run --rm -it --network notes -p 80:80 notesapp:0.1`
    
2. Using a web browser, navigate to the public IP address for the server.
3. Sign up for a new account using an email address and password.
4. Once you are signed up, log in to your account.
5. Create your first note.
6. Verify that you can edit the note.
7. Back in the terminal, disable `Debug mode` by editing the `.env` file:
    
    `vim .env`
    
8. Remove the `export FLASK_ENV='development'` line.
9. Save the file:
    
    `ESC
    :wq`
    
10. Build the image again:
    
    `docker build -t notesapp:0.2 .`
    
11. Run a container using the updated image:
    
    `docker run --rm -it --network notes -p 80:80 notesapp:0.2`
    
12. In a web browser, navigate to the public IP address for the server, and log in to your account.
13. Verify that you can add a second note.
14. In the terminal, stop the container:
    
    `CTRL+C`
    

### Upgrade to Gunicorn

1. Check the `Pipfile`:
    
    `cat Pipfile`
    
2. Run a container and modify the pip file:
    
    `docker run --rm -it -v /home/cloud_user/notes/Pipfile:/tmp/Pipfile notesapp:0.2 bash`
    
3. Once connected, change to the `/tmp` directory:
    
    `cd /tmp`
    
4. Add `gunicorn` to the list of dependencies:
    
    `pipenv install gunicorn`
    
5. Exit the container:
    
    `exit`
    
6. Verify that `gunicorn` was added under `[packages]`:
    
    `cat Pipfile`
    
7. Modify the **init**.py script:
    
    `vim __init__.py`
    
8. Beneath the `import` section, add the following:
    
    `from dotenv import load_dotenv, find_dotenv
    load_dotenv(find_dotenv())`
    
9. Save the file:
    
    `ESC
    :wq`
    
10. Open the `Dockerfile`:
    
    `vim Dockerfile`
    
11. To the bottom of the file, make the following changes:
    
    `COPY . /app/notes
    WORKDIR /app
    EXPOSE 80
    CMD ["gunicorn", "-b 0.0.0.0:80", "notes:create_app()"]`
    
12. Save the file:
    
    `ESC
    :wq`
    

### Build a Production Image

1. Build the updated `notesapp` image:
    
    `docker build -t notesapp:0.3 .`
    
2. Run a detached container using the updated image:
    
    `docker run -d --name notesapp --network notes -p 80:80 notesapp:0.3`
    
3. Check the status of the container:
    
    `docker ps -a`
    
4. In a web browser, navigate to the public IP address for the server, and log in to your account.
5. Verify that you can create a new note.

# LOG

```sh

/ $ ssh cloud_user@3.236.93.87

Host '3.236.93.87' is not in the trusted hosts file.
(ssh-ed25519 fingerprint sha1!! 54:ac:24:79:68:c2:3e:68:95:ef:20:52:b5:b3:01:13:2a:a6:20:f4)
Do you want to continue connecting? (y/n) y
cloud_user@3.236.93.87's password:
[cloud_user@ip-10-0-1-36 ~]$ cd notes
[cloud_user@ip-10-0-1-36 notes]$ ls -la
total 40
drwxr-xr-x. 4 cloud_user cloud_user   155 Mar 28 14:48 .
drwx------. 5 cloud_user cloud_user   153 Mar 28 14:48 ..
-rw-r--r--. 1 cloud_user cloud_user   422 Mar 28 14:48 config.py
-rw-r--r--. 1 cloud_user cloud_user   185 Mar 28 14:48 .env
-rw-r--r--. 1 cloud_user cloud_user  1347 Mar 28 14:48 .gitignore
-rw-r--r--. 1 cloud_user cloud_user  5087 Mar 28 14:48 __init__.py
-rw-r--r--. 1 cloud_user cloud_user   971 Mar 28 14:48 models.py
-rw-r--r--. 1 cloud_user cloud_user   226 Mar 28 14:48 Pipfile
-rw-r--r--. 1 cloud_user cloud_user 10219 Mar 28 14:48 Pipfile.lock
drwxr-xr-x. 2 cloud_user cloud_user   124 Mar 28 14:48 static
drwxr-xr-x. 2 cloud_user cloud_user   165 Mar 28 14:48 templates
[cloud_user@ip-10-0-1-36 notes]$ cat config.py
import os

db_host = os.environ.get('DB_HOST', default='localhost')
db_name = os.environ.get('DB_NAME', default='notes')
db_user = os.environ.get('DB_USERNAME', default='notes')
db_password = os.environ.get('DB_PASSWORD', default='')
db_port = os.environ.get('DB_PORT', default='5432')

SQLALCHEMY_TRACK_MODIFICATIONS = False
SQLALCHEMY_DATABASE_URI = f"postgresql://{db_user}:{db_password}@{db_host}:{db_port}/{db_name}"
[cloud_user@ip-10-0-1-36 notes]$ vim .dockerignore
[cloud_user@ip-10-0-1-36 notes]$ cat .dockerignore
.dockerignore
Dockerfile
.gitignore
Pipfile.lock
migrations/
[cloud_user@ip-10-0-1-36 notes]$

[cloud_user@ip-10-0-1-36 notes]$ vim Dockerfile
[cloud_user@ip-10-0-1-36 notes]$ cat Dockerfile
FROM python:3
ENV PYBASE /pybase
ENV PYTHONUSERBASE $PYBASE
ENV PATH $PYBASE/bin:$PATH
RUN pip install pipenv

WORKDIR /tmp
COPY Pipfile .
RUN pipenv lock
RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile

COPY . /app/notes
WORKDIR /app/notes
EXPOSE 80
CMD ["flask", "run", "--port=80", "--host=0.0.0.0"
[cloud_user@ip-10-0-1-36 notes]$

[cloud_user@ip-10-0-1-36 notes]$ docker build -t notesapp:0.1 .
[+] Building 37.0s (13/13) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                    0.1s
 => => transferring dockerfile: 434B                                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                       0.1s
 => => transferring context: 160B                                                                                                                       0.0s
 => [internal] load metadata for docker.io/library/python:3                                                                                             0.0s
 => [1/8] FROM docker.io/library/python:3                                                                                                               0.2s
 => [internal] load build context                                                                                                                       0.1s
 => => transferring context: 245.85kB                                                                                                                   0.0s
 => [2/8] RUN pip install pipenv                                                                                                                       10.3s
 => [3/8] WORKDIR /tmp                                                                                                                                  0.0s
 => [4/8] COPY Pipfile .                                                                                                                                0.0s
 => [5/8] RUN pipenv lock                                                                                                                              16.3s
 => [6/8] RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile                                                             8.4s
 => [7/8] COPY . /app/notes                                                                                                                             0.1s
 => [8/8] WORKDIR /app/notes                                                                                                                            0.0s
 => exporting to image                                                                                                                                  1.6s
 => => exporting layers                                                                                                                                 1.6s
 => => writing image sha256:07a5383f23d51eb0b42783195391d2eac69d0685f068d3c7c281779041a47dc2                                                            0.0s
 => => naming to docker.io/library/notesapp:0.1                                                                                                         0.0s
[cloud_user@ip-10-0-1-36 notes]$\


```

Run the env

```sh
[cloud_user@ip-10-0-1-36 notes]$ docker images
REPOSITORY   TAG           IMAGE ID       CREATED          SIZE
notesapp     0.1           07a5383f23d5   55 seconds ago   1.06GB
python       3             df3e9d105d6c   5 days ago       921MB
postgres     12.1-alpine   76780864f8de   3 years ago      154MB
[cloud_user@ip-10-0-1-36 notes]$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED       STATUS       PORTS                                       NAMES
2963dad0cdbb   postgres:12.1-alpine   "docker-entrypoint.s…"   3 hours ago   Up 3 hours   0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   notesdb
[cloud_user@ip-10-0-1-36 notes]$ docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
3ee6a140eee0   bridge    bridge    local
46c967f5ee83   host      host      local
6c4e9e045b45   none      null      local
39cbcb072c55   notes     bridge    local
[cloud_user@ip-10-0-1-36 notes]$ docker run --rm -it --network notes -v /home/cloud_user/notes/migrations:/app/notes/migrations notesapp:0.1 bash
root@d580acec37c6:/app/notes# flask db init
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
  Creating directory /app/notes/migrations/versions ...  done
  Generating /app/notes/migrations/README ...  done
  Generating /app/notes/migrations/alembic.ini ...  done
  Generating /app/notes/migrations/env.py ...  done
  Generating /app/notes/migrations/script.py.mako ...  done
  Please edit configuration/connection/logging settings in '/app/notes/migrations/alembic.ini' before proceeding.
root@d580acec37c6:/app/notes# ls -l migrations/
total 16
-rw-r--r--. 1 root root   41 Mar 28 22:00 README
-rw-r--r--. 1 root root  857 Mar 28 22:00 alembic.ini
-rw-r--r--. 1 root root 3228 Mar 28 22:00 env.py
-rw-r--r--. 1 root root  494 Mar 28 22:00 script.py.mako
drwxr-xr-x. 2 root root    6 Mar 28 22:00 versions
root@d580acec37c6:/app/notes# flask db migrate
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.autogenerate.compare] Detected added table 'user'
INFO  [alembic.autogenerate.compare] Detected added table 'note'
  Generating /app/notes/migrations/versions/ee6d529fd3ff_.py ...  done
root@d580acec37c6:/app/notes# flask db upgrade
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
INFO  [alembic.runtime.migration] Context impl PostgresqlImpl.
INFO  [alembic.runtime.migration] Will assume transactional DDL.
INFO  [alembic.runtime.migration] Running upgrade  -> ee6d529fd3ff, empty message
root@d580acec37c6:/app/notes#

root@461f0c3c60c3:/app/notes#
exit
[cloud_user@ip-10-0-1-36 ~]$ cd notes/
[cloud_user@ip-10-0-1-36 notes]$

cloud_user@ip-10-0-1-36 notes]$ docker run --rm -it --network notes -p 80:80 notesapp:0.1
/bin/sh: 1: [flask,: not found

```

Ends with error => missing ']' in dockerfile

```sh
[cloud_user@ip-10-0-1-36 notes]$ docker run --rm -it --network notes -p 80:80 notesapp:0.1
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
 * Serving Flask app '.'
 * Debug mode: on
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:80
 * Running on http://172.18.0.3:80
Press CTRL+C to quit
 * Restarting with stat
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
'FLASK_ENV' is deprecated and will not be used in Flask 2.3. Use 'FLASK_DEBUG' instead.
 * Debugger is active!
 * Debugger PIN: 264-163-232

188.167.250.18 - - [28/Mar/2023 22:18:08] "GET / HTTP/1.1" 302 -
188.167.250.18 - - [28/Mar/2023 22:18:08] "GET /notes HTTP/1.1" 302 -
188.167.250.18 - - [28/Mar/2023 22:18:09] "GET /log_in HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:09] "GET /static/bulma.min.css HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:09] "GET /static/styles.css HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:09] "GET /static/app.js HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:09] "GET /static/highlight.min.css HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:09] "GET /static/highlight.min.js HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:10] "GET /favicon.ico HTTP/1.1" 404 -
188.167.250.18 - - [28/Mar/2023 22:18:28] "GET /sign_up HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:29] "GET /static/bulma.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:29] "GET /static/highlight.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:29] "GET /static/highlight.min.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:29] "GET /static/styles.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:29] "GET /static/app.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:44] "POST /sign_up HTTP/1.1" 302 -
188.167.250.18 - - [28/Mar/2023 22:18:44] "GET /log_in HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:45] "GET /static/bulma.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:45] "GET /static/styles.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:45] "GET /static/highlight.min.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:45] "GET /static/highlight.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:45] "GET /static/app.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:58] "POST /log_in HTTP/1.1" 302 -
188.167.250.18 - - [28/Mar/2023 22:18:58] "GET / HTTP/1.1" 302 -
188.167.250.18 - - [28/Mar/2023 22:18:58] "GET /notes HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:18:59] "GET /static/bulma.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:59] "GET /static/highlight.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:59] "GET /static/highlight.min.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:59] "GET /static/styles.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:18:59] "GET /static/app.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:01] "GET /notes/new HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:19:02] "GET /static/bulma.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:02] "GET /static/highlight.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:02] "GET /static/styles.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:02] "GET /static/highlight.min.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:02] "GET /static/app.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:21] "POST /notes/new HTTP/1.1" 302 -
188.167.250.18 - - [28/Mar/2023 22:19:22] "GET /notes HTTP/1.1" 200 -
188.167.250.18 - - [28/Mar/2023 22:19:22] "GET /static/bulma.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:22] "GET /static/highlight.min.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:22] "GET /static/styles.css HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:22] "GET /static/highlight.min.js HTTP/1.1" 304 -
188.167.250.18 - - [28/Mar/2023 22:19:22] "GET /static/app.js HTTP/1.1" 304 -


cloud_user@ip-10-0-1-36 notes]$ vim .env
[cloud_user@ip-10-0-1-36 notes]$ docker build -t notesapp:0.2 .
[+] Building 0.2s (13/13) FINISHED
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 435B                                                                                                                    0.0s
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 160B                                                                                                                       0.0s
 => [internal] load metadata for docker.io/library/python:3                                                                                             0.0s
 => [internal] load build context                                                                                                                       0.0s
 => => transferring context: 2.08kB                                                                                                                     0.0s
 => [1/8] FROM docker.io/library/python:3                                                                                                               0.0s
 => CACHED [2/8] RUN pip install pipenv                                                                                                                 0.0s
 => CACHED [3/8] WORKDIR /tmp                                                                                                                           0.0s
 => CACHED [4/8] COPY Pipfile .                                                                                                                         0.0s
 => CACHED [5/8] RUN pipenv lock                                                                                                                        0.0s
 => CACHED [6/8] RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile                                                      0.0s
 => [7/8] COPY . /app/notes                                                                                                                             0.0s
 => [8/8] WORKDIR /app/notes                                                                                                                            0.0s
 => exporting to image                                                                                                                                  0.1s
 => => exporting layers                                                                                                                                 0.1s
 => => writing image sha256:9e74d030bda1549f3123dc7be6d8e6d59f7272c910cad35dd4b9334000eda793                                                            0.0s
 => => naming to docker.io/library/notesapp:0.2                           


 [cloud_user@ip-10-0-1-36 notes]$ docker run --rm -it --network notes -p 80:80 notesapp:0.2
 * Serving Flask app '.'
 * Debug mode: off
WARNING: This is a development server. Do not use it in a production deployment. Use a production WSGI server instead.
 * Running on all addresses (0.0.0.0)
 * Running on http://127.0.0.1:80
 * Running on http://172.18.0.3:80
Press CTRL+C to quit

[cloud_user@ip-10-0-1-36 notes]$
[cloud_user@ip-10-0-1-36 notes]$
[cloud_user@ip-10-0-1-36 notes]$ cat Pipfile
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]
python-dotenv = "*"

[packages]
flask = "*"
flask-sqlalchemy = "*"
psycopg2-binary = "*"
flask-migrate = "*"
mistune = "*"

[requires]
[cloud_user@ip-10-0-1-36 notes]$ docker run --rm -it -v /home/cloud_user/notes/Pipfile:/tmp/Pipfile notesapp:0.2 bash
root@fed041120c8a:/app/notes# cd /tmp
root@fed041120c8a:/tmp# pip
pip              pip3             pip3.11          pipenv           pipenv-resolver
root@fed041120c8a:/tmp# pipenv install gunicorn
Installing gunicorn...
Resolving gunicorn...
Installing...
Adding gunicorn to Pipfile's [packages] ...
✔ Installation Succeeded
Pipfile.lock (730408) out of date, updating to (436a86)...
Locking [packages] dependencies...
Building requirements...
Resolving dependencies...
✔ Success!
Locking [dev-packages] dependencies...
Building requirements...
Resolving dependencies...
✔ Success!
Updated Pipfile.lock (910c2b59e2dfe1be1bbaea607ea4a40e6d4287cac43990da61f0b75caf436a86)!
Installing dependencies from Pipfile.lock (436a86)...
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
root@fed041120c8a:/tmp#
exit

[cloud_user@ip-10-0-1-36 notes]$ cat Pipfile
[[source]]
name = "pypi"
url = "https://pypi.org/simple"
verify_ssl = true

[dev-packages]
python-dotenv = "*"

[packages]
flask = "*"
flask-sqlalchemy = "*"
psycopg2-binary = "*"
flask-migrate = "*"
mistune = "*"
gunicorn = "*"

[requires]
[cloud_user@ip-10-0-1-36 notes]$

[cloud_user@ip-10-0-1-36 notes]$ vim __init__.py
[cloud_user@ip-10-0-1-36 notes]$ vim Dockerfile
[cloud_user@ip-10-0-1-36 notes]$ docker build -t notesapp:0.3 .
[+] Building 25.8s (13/13) FINISHED
 => [internal] load .dockerignore                                                                                                                       0.0s
 => => transferring context: 160B                                                                                                                       0.0s
 => [internal] load build definition from Dockerfile                                                                                                    0.0s
 => => transferring dockerfile: 439B                                                                                                                    0.0s
 => [internal] load metadata for docker.io/library/python:3                                                                                             0.0s
 => [1/8] FROM docker.io/library/python:3                                                                                                               0.0s
 => [internal] load build context                                                                                                                       0.0s
 => => transferring context: 7.33kB                                                                                                                     0.0s
 => CACHED [2/8] RUN pip install pipenv                                                                                                                 0.0s
 => CACHED [3/8] WORKDIR /tmp                                                                                                                           0.0s
 => [4/8] COPY Pipfile .                                                                                                                                0.0s
 => [5/8] RUN pipenv lock                                                                                                                              16.9s
 => [6/8] RUN PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile                                                             8.0s
 => [7/8] COPY . /app/notes                                                                                                                             0.0s
 => [8/8] WORKDIR /app/notes                                                                                                                            0.0s
 => exporting to image                                                                                                                                  0.7s
 => => exporting layers                                                                                                                                 0.7s
 => => writing image sha256:f149a44ea01f85f86f1faf301b7735efea20340f089e67d6fd562461d6673c90                                                            0.0s
 => => naming to docker.io/library/notesapp:0.3                                       

 [cloud_user@ip-10-0-1-36 notes]$ docker run -d --name notesapp --network notes -p 80:80 notesapp:0.3
113a0459a6cb545d76b0c1d7744b56b4434fadbf508b13412167b40cf5a6cb3f
[cloud_user@ip-10-0-1-36 notes]$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS                     PORTS                                       NAMES
113a0459a6cb   notesapp:0.3           "gunicorn '-b 0.0.0.…"   5 seconds ago   Exited (3) 3 seconds ago                                               notesapp
2963dad0cdbb   postgres:12.1-alpine   "docker-entrypoint.s…"   4 hours ago     Up 4 hours                 0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   notesdb
[cloud_user@ip-10-0-1-36 notes]$
 ```

 Error

 ```sh
[cloud_user@ip-10-0-1-36 notes]$ docker ps -a
CONTAINER ID   IMAGE                  COMMAND                  CREATED         STATUS                     PORTS                                       NAMES
113a0459a6cb   notesapp:0.3           "gunicorn '-b 0.0.0.…"   5 seconds ago   Exited (3) 3 seconds ago                                               notesapp
2963dad0cdbb   postgres:12.1-alpine   "docker-entrypoint.s…"   4 hours ago     Up 4 hours                 0.0.0.0:5432->5432/tcp, :::5432->5432/tcp   notesdb
[cloud_user@ip-10-0-1-36 notes]$ docker logs notesapp
[2023-03-28 22:28:42 +0000] [1] [INFO] Starting gunicorn 20.1.0
[2023-03-28 22:28:42 +0000] [1] [INFO] Listening at: http://0.0.0.0:80 (1)
[2023-03-28 22:28:42 +0000] [1] [INFO] Using worker: sync
[2023-03-28 22:28:42 +0000] [7] [INFO] Booting worker with pid: 7
[2023-03-28 22:28:42 +0000] [7] [ERROR] Exception in worker process
Traceback (most recent call last):
  File "/pybase/lib/python3.11/site-packages/gunicorn/arbiter.py", line 589, in spawn_worker
    worker.init_process()
  File "/pybase/lib/python3.11/site-packages/gunicorn/workers/base.py", line 134, in init_process
    self.load_wsgi()
  File "/pybase/lib/python3.11/site-packages/gunicorn/workers/base.py", line 146, in load_wsgi
    self.wsgi = self.app.wsgi()
                ^^^^^^^^^^^^^^^
  File "/pybase/lib/python3.11/site-packages/gunicorn/app/base.py", line 67, in wsgi
    self.callable = self.load()
                    ^^^^^^^^^^^
  File "/pybase/lib/python3.11/site-packages/gunicorn/app/wsgiapp.py", line 58, in load
    return self.load_wsgiapp()
           ^^^^^^^^^^^^^^^^^^^
  File "/pybase/lib/python3.11/site-packages/gunicorn/app/wsgiapp.py", line 48, in load_wsgiapp
    return util.import_app(self.app_uri)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/pybase/lib/python3.11/site-packages/gunicorn/util.py", line 359, in import_app
    mod = importlib.import_module(module)
          ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "/usr/local/lib/python3.11/importlib/__init__.py", line 126, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
           ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
  File "<frozen importlib._bootstrap>", line 1206, in _gcd_import
  File "<frozen importlib._bootstrap>", line 1178, in _find_and_load
  File "<frozen importlib._bootstrap>", line 1142, in _find_and_load_unlocked
ModuleNotFoundError: No module named 'notes'
[2023-03-28 22:28:42 +0000] [7] [INFO] Worker exiting (pid: 7)
[2023-03-28 22:28:42 +0000] [1] [INFO] Shutting down: Master
[2023-03-28 22:28:42 +0000] [1] [INFO] Reason: Worker failed to boot.

 ```

 Error was workdir => must be /app, not /app/notes !!!

 HUUGE

 ```sh
[cloud_user@ip-10-0-1-36 notes]$ docker images
REPOSITORY   TAG           IMAGE ID       CREATED              SIZE
notesapp     0.3           d177fa2b18e2   About a minute ago   1.06GB
<none>       <none>        f149a44ea01f   12 minutes ago       1.06GB
notesapp     0.2           9e74d030bda1   18 minutes ago       1.06GB
<none>       <none>        07a5383f23d5   40 minutes ago       1.06GB
notesapp     0.1           110eb2e2be77   40 minutes ago       1.06GB
python       3             df3e9d105d6c   5 days ago           921MB
postgres     12.1-alpine   76780864f8de   3 years ago          154MB
 ```