To-do-list-Project
A Django-based to-do app is a web application that allows users to create, manage, and track their to-do items. Django is a Python web framework that makes it easy to develop and maintain web applications.

Initially, I tested the application using Python's migrate and runserver commands. Later, I attempted containerizing it with Docker, but managing the Docker containers became cumbersome. Each time I wanted to deploy a new version, I had to delete the old Docker container, which was tedious. To simplify the process and eliminate the need for manual deletion of the old containers to free up port 8000 for Django, I transitioned to using Docker Compose. This shift allowed for a seamless deployment experience without the hassle of managing previous containers.

Jenkins Setup: Jenkins and Docker are integrated, forming the core of the automation process.

Pipeline Configuration: Utilizes Jenkins' Execute Shell to run Docker Compose commands in the pipeline.

This project streamlines the deployment of Django applications, offering an efficient and scalable solution.

Technologies Used:
Docker Compose: Simplifies multi-container application deployment.

Jenkins: Employs automation for continuous integration and deployment.

GitHub: Houses the application's source code.

Workflow:
Developers modify index.html and trigger the Jenkins pipeline.

The pipeline fetches the GitHub project, creating a Docker Compose file for deployment.

The application deploys automatically, eliminating manual intervention.

Future Addition
Scalability: The pipeline can be extended via a Jenkinsfile, accommodating additional steps and complex deployment scenarios.
