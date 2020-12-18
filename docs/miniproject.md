# Notes App

Migrating static content into containers was a great way to learn the basics of Docker, but real-world uses are usually dynamic, including full applications.

This mini-project will show you the process to dockerize a Flask application. Flask is a lightweight Python WSGI micro web framework, however, you won't need to know any Python to complete this mini-project.

The Flask application for the following exercise is located here : `/kubernetes-cloud-caas-course/src/app/notes`. The following commands will assume this as the source directory.

## Database & Network Configuration

The Flask Application communicate with a postgres database to work.

1. Create a `notes` network in docker which will contains our application.

2. Run a postgres database from this image `postgres:12.1-alpine`:

- You have to expose the same default port of postgres into the host
- You have to define the DB Name, User and password when you run the container as an environment variable. **You can find the values of this variables by inspecting the `file.env` file**
- The name of the container must be `notesdb`
- The container must be run in a `notes` network of type bridge

## Create Dockerfile for the application

1. Inspect the Flask application files to learn what files we need to include and exclude from the image. There is a `Pipfile.lock`, a `.gitignore`, and a `migrations` directory. We don't want those in the image.

**N.B: Before adding file to dockerfile you have to rename `file.env` to `.env`**

2. Create a `.dockerignore` file to exclude build and metadata information from the image.

3. Create the `Dockerfile` to build the image.

- Start with Python 3 as the base image.
- Setup Python environment variables:

  - PYBASE /pybase
  - PYTHONUSERBASE $PYBASE
  - PATH $PYBASE/bin:$PATH

- Install dependencies in the container from the Pipfile:

  - execute the command `pip install pipenv`
  - change the temporary workdir to /tmp
  - copy the pipefile into /tmp
  - execute the command `pipenv lock`
  - execute the command `PIP_USER=1 PIP_IGNORE_INSTALLED=1 pipenv install -d --system --ignore-pipfile`

- Copy the source files of the application to `/app/notes`
- Set the working directory to `/app/notes`
- Specify the port flask will listen on `80`
- Start the flask server `flask run --port=80 --host=0.0.0.0`

## Build and Setup Environment

1. Build an image named `notesapp` with version `0.1`.

2. Inspect the Docker environment to see the new image, and what else is running.
3. Use a container of the `notesapp` image to set up the database:
   `docker run --rm -it --network notes -v /home/cloud_user/notes/migrations:/app/notes/migrations notesapp:0.1 bash`

- **Note**: The database can be set up via the app itself using the following commands:
  - `flask db init`
  - `flask db migrate`
  - `flask db upgrade`

## Run, Evaluate

1. Run the `notesapp` container in the current terminal to watch its output.
2. View the application in a browser. Sign up for an account, create a note, then edit it.
3. View the messages in the terminal and check for warnings.

## Clean environment

1. Stop the two containers
2. Delete the created network and clean up the environment

## Docker compose

1. Create a `docker-compose.yml` file for our application that will:

- Create a network for our application
- Run the database container with the same configuration we did before
- Run the flask app container **after** the database container

**Optional**: You can create a script to configure the database or simply connect to the container and run the commands as we did above.

2. Verify that the application works well

## Kubernetes

In this last section we will deploy this application in a kubernetes cluster:

1. A secret for the database password
2. A configmap for the other env variables
3. You have to create a deployment for the flask application
4. A nodeport type service for the flask application
5. A Statefulset for the databse
6. A ClusterIP service for the database
