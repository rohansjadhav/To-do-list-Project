
# To-do-list-Project
This project involved hosting a Django-based web application using Docker Compose and setting up a CI/CD pipeline using Jenkins and GitHub. The CI/CD pipeline was designed to deploy the application to a local Docker Compose environment whenever changes were made to the GitHub repository following the execution of the pipeline.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
To host django app on local.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### Setup
To get this repository, run the following command inside your git enabled terminal
```bash
$ git clone https://github.com/shreys7/django-todo.git
```
You will need django to be installed in you computer to run this app. Head over to https://www.djangoproject.com/download/ for the download guide

Once you have downloaded django, go to the cloned repo directory and run the following command

```bash
$ python manage.py makemigrations
```

This will create all the migrations file (database migrations) required to run this App.

Now, to apply this migrations run the following command
```bash
$ python manage.py migrate
```

One last step and then our todo App will be live. We need to create an admin user to run this App. On the terminal, type the following command and provide username, password and email for the admin user
```bash
$ python manage.py createsuperuser
```

That was pretty simple, right? Now let's make the App live. We just need to start the server now and then we can start using our simple todo App. Start the server by following command

```bash
$ python manage.py runserver
```

![logs of deployment on local](https://github.com/rohansjadhav/To-do-list-Project/assets/149859730/9f0e90eb-4485-4548-a220-e222ab1eef47)

Once the server is hosted, head over to http://localhost:8000/todos for the App.

Below is the screenshot showing the app hosted on local on port 8000

![deployed on local with python runserver](https://github.com/rohansjadhav/To-do-list-Project/assets/149859730/ec72cb0e-4bed-4b33-a42e-ed543190d4f7)

After successfully testing and hosting the application on local, let's start containerizing it.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
To host a Django app on Docker Compose.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### Setup
We first need to create a Dockerfile. Then, you need to mention the Dockerfile in the docker-compose.yaml file.


```bash
#Using base image as Python
FROM python:3

#Installing django using pip install
RUN pip install django

#Copying all the repo contianining application related file inside container
COPY . .

#Migrate executes those SQL commands in the database file before running runserver.
RUN python /manage.py migrate

#Expose port 8000 to accessed the app from outside of the container
EXPOSE 8000

#Command to run the django server at localhost on port 8000
CMD ["python", "manage.py", "runserver", "0.0.0.0:8000"] 
```

After creating a docker file inside the repo create a docker-compose.yaml to build the dockerfile and run the application.

```bash
version: '3'
services:
  django-app:
    build: .
    ports:
      - "8000:8000"
```

Before running the Docker Compose file with Jenkins, verify that the app can be hosted with Docker Compose by running docker-compose up -d on the local repository and checking if the application is hosted locally. In my case, this worked just fine.

If you encounter any issues such as server running or permission denied while using Docker as another user, run the following command to resolve the issue:

```bash
# Add the current user to the docker group
sudo usermod -aG docker $USER

# Change the permissions of the Docker socket to the current user
sudo chown $USER /var/run/docker.sock
```

We will run the application using Jenkins pipeline now.

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Installation of Jenkins.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
### Setup
```bash
yum install java-11-openjdk -y
curl --silent --location http://pkg.jenkins-ci.org/redhat-stable/jenkins.repo | sudo tee /etc/yum.repos.d/jenkins.repo
sudo rpm --import https://jenkins-ci.org/redhat/jenkins-ci.org.key
sudo yum install jenkins
systemctl start jenkins
systemctl enable jenkins
```
Before setting up the pipeline, we need to connect Jenkins to GitHub directly to the repository file. This will allow developers to make changes directly on GitHub and then trigger the Jenkins pipeline.

As shown below, enter the repository URL and create credentials in Dashboard > Manage Jenkins > Credentials. Then, select those credentials from the dropdown menu and change the branch to develop and save it. 

![image](https://github.com/rohansjadhav/To-do-list-Project/assets/149859730/0e64c828-037c-4642-883c-d6e8ca9f90f5)

In Jenkins, create a freestyle job and go to configuration. Under Build Steps, select Execute shell and paste the following Docker Compose commands to run the Docker Compose file. Then, save the job.

```bash
docker-compose down
docker-compose up -d --force-recreate --no-deps --build django-app
```


![image](https://github.com/rohansjadhav/To-do-list-Project/assets/149859730/3d5b6538-4f77-491f-849a-e71b6da961d3)

Click Build now to run the pipeline. Once the pipeline finishes, the logs will show that it built the Dockerfile and ran the application as Docker Compose.

![image](https://github.com/rohansjadhav/To-do-list-Project/assets/149859730/8e862ceb-1853-4768-bf50-ac01f8f86c4c)

I changed the title in index.html before running the pipeline, and the change was reflected in the hosted application.

![image](https://github.com/rohansjadhav/To-do-list-Project/assets/149859730/427075ec-6531-4ea9-8a1a-ba6d8ed0dbfa)

------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
Future Scope.
------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
1. Use Kubernetes to deploy the application for high availability.
2. Use Jenkinsfile for the Jenkins pipeline.
3. Use the Docker repository or AWS ECR to save the Images.
4. Set up a DNS record to access the application.
