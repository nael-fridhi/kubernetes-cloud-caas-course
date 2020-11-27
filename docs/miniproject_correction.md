
# Note: 
- You have to run a db container `postgres:12.1-alpine` with the name `notesdb` and with env `POSTGRES_USER=demo` and `POSTGRES_PASSWORD=secure_password`
- Create database `notes`
- Create a docker bridge network `notes`


Create the .dockerignore file:
```
.dockerignore
Dockerfile
.gitignore
Pipfile.lock
migrations/
```
Create the Dockerfile:
```
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
CMD ["flask", "run", "--port=80", "--host=0.0.0.0"]
``` 

Build the notesapp image:
`docker build -t notesapp:0.1 .`

List the containers:
`docker ps -a`
List the docker networks:
`docker network ls`
Run a container using the notesapp image and mount the migrations directory:
`docker run --rm -it --network notes -v /home/cloud_user/notes/migrations:/app/notes/migrations notesapp:0.1 bash`
Once connected to the container, enable SQLAlchemy:
`flask db init`
Check the migrations folder:
`ls -l migrations`
Create the files needed to configure the database:
`flask db migrate`
Apply the files:
`flask db upgrade`

Run, Evaluate, and Upgrade
Run a container using the notesapp:0.1 image:
`docker run --rm -it --network notes -p 80:80 notesapp:0.1`
Using a web browser, to see the application. Sign up for a new account using an email address and password. Once you are signed up, log in to your account. Create your first note. Verify that you can edit the note.

