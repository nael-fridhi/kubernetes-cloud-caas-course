# Dockerize Notes Flask App

1. Inspect the Flask application files to learn what files we need to include and exclude from the image. There is a Pipfile.lock, a .gitignore, and a migrations directory. We don't want those in the image.
2. Create a .dockerignore file to exclude build and metadata information from the image.
3. Create the Dockerfile to build the image.
  - Start with Python 3 as the base image.
  - Setup Python environment variables.
  - Install dependencies in the container from the Pipfile.
  - Set the working directory.
  - Specify the port flask will listen on.
  - Start the flask server.
  - check_circle


## Build and Setup Environment
1. Build an image named notesapp with version 0.1.
2. Inspect the Docker environment to see the new image, and what else is running.
Use a container of the notesapp image to set up the database.

**Note**: The database can be set up via the app itself using the following command.
  `flask db init && flask db migrate && flask db upgrade`

## Run, Evaluate, and upgrade 

1. Run the NotesApp container in the current terminal to watch its output.
2. View the application in a browser. Sign up for an account, create a note, then edit it.
3. View the messages in the terminal and check for warnings.
4. Stop using Flask in debug mode.
  Remove the line FLASK_ENV=development from the file ~/notes/.env.
5. Since we've changed a file included in the image, build the image again as version 0.2.
6. Run the new version of the NotesApp image in the terminal. Debug mode should no longer be on.
7. Look for any new warnings.

**Note**: We're not in debug anymore, but now Flask tells us "Do not use it in a production deployment". That's not a good way to leave our container.

8. Stop the container.


## Upgrade to Gunicorn

We will upgrade our Flask application to use Gunicorn, which is a production-ready WSGI HTTP server. This will involve adding a bit of code to our existing app and upgrading our build.

1. Add Gunicorn to the dependencies using pipenv.
  Pipenv is being used to manage the dependencies for our application. Let's use the image we have, which includes pipenv, to upgrade the Pipfile so we don't have to install a development environment on the server.
2.  Upgrade the application code for Gunicorn.
  Flask can use dotenv automatically, but Gunicorn must be told to use it explicitly. Add the following lines to __init__.py below the import statements:
``` 
from dotenv import load_dotenv, find_dotenv
load_dotenv(find_dotenv())
```
3. Change the Dockerfile to run Gunicorn instead of Flask.
  Replace the WORKDIR and CMD in the Dockerfile with the below code:
```
WORKDIR /app
CMD [ "gunicorn", "-b 0.0.0.0:80", "notes:create_app()"]
```

## Build a Production Image

Build the NotesApp image again. Remember to increment the version.
Run a container from the production image, this time using detached mode to run in the background.
Inspect the Docker environment to ensure everything is running.
View the application in a browser.