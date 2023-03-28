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
