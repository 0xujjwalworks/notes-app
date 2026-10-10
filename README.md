Notes App

A containerized notes application built with Django, Python, MySQL, and Nginx. This repository also includes a Dockerfile, Docker Compose configuration, and CI/CD-related files for learning and experimenting with deployment workflows.

Project status: This README documents the repository structure and the Docker Compose setup currently present in the project. Update the feature list and deployment details as the application evolves.

Table of Contents

Overview

Features and Components

Technology Stack

Architecture

Project Structure

Prerequisites

Run with Docker Compose

Run with Docker

Run Locally with Python

Useful Docker Commands

CI/CD

Troubleshooting

Security Notes

Future Improvements

Contributing

Author

License

Overview

The Notes App is a web application project packaged for container-based development and deployment. Django runs the application, MySQL provides relational database storage, and Nginx acts as the web-facing reverse proxy.

Docker Compose brings the application services up together, making it easier to run the project consistently on a local machine or a Linux server.

Features and Components

Django application for the web application and backend logic.

MySQL database for persistent relational data.

Nginx reverse proxy to receive HTTP requests and forward them to the application.

Docker image build using a multi-stage Dockerfile.

Docker Compose to coordinate the application, database, and Nginx services.

Persistent database volume so database files can survive container recreation.

GitHub Actions / Jenkins files included in the repository for CI/CD experimentation.

Technology Stack

Technology

Purpose

Python 3.11

Application runtime in the Dockerfile

Django

Web framework

MySQL 8.0

Relational database

Gunicorn

WSGI application server

Nginx

Web server and reverse proxy

Docker

Containerization

Docker Compose

Multi-container orchestration

GitHub Actions / Jenkins

CI/CD workflow experimentation

Architecture

Browser
   |
   | HTTP :80
   v
Nginx container
   |
   | forwards requests to Django
   v
Django + Gunicorn container
   |
   | MySQL connection on the internal Compose network
   v
MySQL container
   |
   v
Persistent Docker volume (mysql_data)

The database is intended to be accessed by the application through the internal Docker Compose network, rather than being exposed directly to the host.

Project Structure

notes-app/
├── .github/
│   └── workflows/       # GitHub Actions workflow files
├── api/                 # API-related application code
├── mynotes/             # Notes application module
├── notesapp/             # Django project configuration
├── nginx/               # Nginx Docker configuration
├── staticfiles/          # Collected static assets
├── Dockerfile            # Django application image
├── docker-compose.yml    # Application, MySQL, and Nginx services
├── Jenkinsfile           # Jenkins pipeline definition
├── manage.py             # Django management utility
├── requirements.txt      # Python dependencies
└── README.md

The exact contents of folders may change as the project develops.

Prerequisites

Before starting, install:

Git

Docker Desktop on Windows/macOS, or Docker Engine on Linux

Docker Compose v2 (docker compose)

You do not need to install Python or MySQL separately when using Docker Compose.

Run with Docker Compose (Recommended)

1. Clone the repository

git clone https://github.com/0xujjwalworks/notes-app.git
cd notes-app

2. Review environment configuration

The Compose file references a .env file. Check the expected variables and configuration before starting the project.

If the repository does not contain a safe example file, create .env.example with placeholder values and copy it to .env:

cp .env.example .env

On Windows PowerShell, use:

Copy-Item .env.example .env

Use the variable names expected by the application settings. Do not put real production credentials in .env.example.

3. Build and start the services

docker compose up --build -d

4. Check service status

docker compose ps

View logs:

docker compose logs -f

View logs for one service:

docker compose logs -f django_app
docker compose logs -f db
docker compose logs -f nginx

5. Open the application

Once the services are running, open:

http://localhost

The Compose configuration maps host port 80 to Nginx. If port 80 is already in use, update the port mapping in docker-compose.yml (for example, "8080:80") and open http://localhost:8080.

6. Stop the application

Stop and remove the containers and network:

docker compose down

To also remove the database volume and permanently delete the database data stored in it:

docker compose down -v

Warning: docker compose down -v deletes the Compose-managed database volume. Back up important data before running it.

Run with Docker

If you want to build and run only the application image:

docker build -t notes-app .
docker run --rm -p 8000:8000 notes-app

Then visit http://localhost:8000.

Note: The Django application may require MySQL and environment variables. If those are required by the current settings, use Docker Compose instead, because it starts the application and database together.

Run Locally with Python

Use this method if you want to develop without building a container. You will need a compatible Python version, the dependencies from requirements.txt, and a running database configured for the Django settings.

Create and activate a virtual environment:

python -m venv .venv

Linux/macOS:

source .venv/bin/activate

Windows PowerShell:

.\.venv\Scripts\Activate.ps1

Install dependencies:

python -m pip install --upgrade pip
pip install -r requirements.txt

Configure the database and environment variables for your local setup, then run Django:

python manage.py migrate
python manage.py runserver

Open http://127.0.0.1:8000.

Useful Docker Commands

Task

Command

Build and start services

docker compose up --build -d

List running services

docker compose ps

Follow all logs

docker compose logs -f

Follow Django logs

docker compose logs -f django_app

Restart services

docker compose restart

Stop services

docker compose down

Rebuild after Dockerfile changes

docker compose build --no-cache

List images

docker images

List containers

docker ps -a

Open a shell in the Django container

docker compose exec django_app sh

CI/CD

This repository includes GitHub Actions workflow files and a Jenkinsfile. These can be used to practise automating tasks such as building and validating the application image.

A typical CI/CD workflow can:

Trigger when code is pushed or a pull request is opened.

Check out the repository.

Build the Docker image.

Run available checks or tests.

Optionally authenticate to a container registry and push the image.

Optionally deploy the application to a configured server or hosting platform.

Important: Building or pushing an image does not automatically make the website publicly accessible. A deployment step and a reachable hosting environment are needed for that. Check the actual workflow and Jenkins pipeline files to see which steps are configured.

Troubleshooting

Port 80 is already in use

Change the Nginx port mapping in docker-compose.yml, for example:

ports:
  - "8080:80"

Then restart the services:

docker compose up --build -d

A container exits or stays unhealthy

Check service status and logs:

docker compose ps
docker compose logs --tail=100

Django cannot connect to MySQL

Confirm the database service is running: docker compose ps.

Check the database logs: docker compose logs db.

Make sure the Django database host uses the Compose service name db, not localhost.

Verify that database name, username, password, and port match between Django configuration and Compose.

Allow a short time for MySQL to initialize before the application connects.

Changes are not appearing

Rebuild and recreate the services:

docker compose up --build -d

If necessary, inspect the logs to check for build or startup errors.

Security Notes

Never commit production passwords, cloud credentials, API tokens, or private keys.

Keep real environment files out of Git and provide a sanitized .env.example instead.

Replace example database passwords before deploying anywhere beyond local development.

Avoid exposing the MySQL port publicly unless there is a specific, secured requirement.

Use HTTPS and appropriate security settings before making the application publicly accessible.

Keep dependencies and base images updated, and review security alerts regularly.

Future Improvements

Add automated tests and a documented test command.

Add a sanitized .env.example and document required variables.

Add application and database health checks where appropriate.

Add a CI workflow that builds and validates the Docker image on pull requests.

Publish versioned images to a container registry.

Document a repeatable deployment process with HTTPS and secret management.

Add screenshots or a demo link once a stable deployed version is available.

Contributing

Contributions and suggestions are welcome.

Fork the repository.

Create a branch: git checkout -b feature/your-change.

Make and test your changes.

Commit: git commit -m "Describe your change".

Push your branch and open a pull request.

Author

Ujjwal Gupta

GitHub: @0xujjwalworks

License

Check the LICENSE file for the license terms. If no license file exists, add one before presenting the project as open source.
