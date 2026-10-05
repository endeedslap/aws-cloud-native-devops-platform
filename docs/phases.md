Build Phases

This is my running log of the project. I’m documenting what I worked on, why I did it, what I learned, and how I verified that it worked.

Phase 1 — Application

Goal: Build a small Flask application that I can eventually deploy to Kubernetes.

What I built

I created a Flask application in app.py with four main endpoints:

/ — the main application page

/health — checks if the application is healthy

/version — shows the current application version

/metrics — provides metrics for Prometheus

I also added:

JSON logging using python-json-logger

Prometheus metrics using prometheus-flask-exporter

Custom error handling for 404 and 500 errors

pytest tests for the application endpoints

pyproject.toml to configure pytest

Why I built it this way

I wanted to start with an application that already has some of the pieces needed for production.

The /health endpoint will be useful later when Kubernetes needs to check whether the application is running correctly.

The /version endpoint gives me an easy way to see which version of the application is running.

The /metrics endpoint will eventually be used by Prometheus for monitoring.

I also used environment variables for configuration so that I can change settings without changing the application code.

What I verified

I ran:

pytest -v


The tests passed.

I also tested the application endpoints through HTTP and confirmed that the application returns the expected responses.

The application logs are also being written in JSON format.

Phase 2 — Git & GitHub

Goal: Put the project under Git version control and learn how to use GitHub with a proper Pull Request workflow.

What I did

I initialized Git in the project and created a .gitignore file so things like the Python virtual environment, Terraform files, and environment files aren't accidentally committed.

I created the GitHub repository:

new-devops-project

I made my first commit and pushed the project to GitHub.

I also configured Git credentials so I wouldn't have to enter my GitHub credentials every time I pushed.

GitHub Ruleset

I added a ruleset to the main branch.

The ruleset requires changes to go through a Pull Request instead of allowing direct pushes to main.

It also prevents force pushes and branch deletion.

I created a feature branch and merged a Pull Request to make sure the workflow actually worked.

What I learned

The main reason for using Pull Requests is to keep main stable.

Even though this is currently a personal project, I'm trying to follow the same workflow I would expect to use on a development team.

The idea is:

Create branch
      |
      v
Make changes
      |
      v
Commit
      |
      v
Push branch
      |
      v
Pull Request
      |
      v
Merge into main

What I verified

I tested a direct push to main and GitHub rejected it because of the repository rules.

I also successfully created and merged a Pull Request.

Phase 3 — Docker

Goal: Package the Flask application into a Docker container so it can run consistently in different environments.

What I built

I created a Dockerfile using:

python:3.12-slim


I chose the slim image because I don't need a full operating system image for this application.

I also added:

PYTHONUNBUFFERED=1 so logs show up immediately

PYTHONDONTWRITEBYTECODE=1 so Python doesn't create unnecessary .pyc files

Docker layer caching for the Python dependencies

A non-root user called app

A Docker HEALTHCHECK

Gunicorn to run the Flask application

I also created a .dockerignore file to keep unnecessary files such as .git, .venv, tests, documentation, and environment files out of the Docker image.

Why I used Gunicorn

I didn't use Flask's development server for the container.

The Flask development server is meant for development and testing, not for running a production application.

Gunicorn is a production WSGI server and is a better fit for the container.

Why I used a non-root user

The container runs as the app user instead of root.

This is a security improvement because if something inside the application is compromised, the process doesn't automatically have root privileges inside the container.

Why I didn't use a multi-stage build

I looked at using a multi-stage Docker build, but I don't currently need it.

The dependencies I'm using don't require a complicated compilation step, so adding multiple build stages would make the Dockerfile more complicated without giving me much benefit.

For this project, keeping the Dockerfile simple makes more sense.

What I verified

I built the Docker image and started the container.

I checked that the container was running and that the health check worked.

I tested the application endpoints through the container.

I also ran:

docker exec flask-test whoami


and confirmed that the container is running as:

app


I checked the logs with:

docker logs flask-test


and confirmed that Gunicorn starts correctly and the application produces JSON log messages.

Phase 4 — AWS Networking

Status: In progress

Goal: Understand the AWS networking concepts before creating the infrastructure with Terraform.

This phase is mostly about understanding how AWS networking works rather than writing code.

Topics I'm learning

AWS Regions

Availability Zones

VPCs

CIDR ranges

Public subnets

Private subnets

Internet Gateways

NAT Gateways

Route tables

Security Groups

Network ACLs

Multi-AZ architecture

What I'm trying to understand

I'm focusing on the reason behind each AWS networking component instead of just memorizing definitions.

Some of the questions I'm working through are:

Why do I need a VPC?

Why should worker nodes normally be in private subnets?

Why would a private subnet need a NAT Gateway?

What is the difference between a Security Group and a Network ACL?

Why should the infrastructure use more than one Availability Zone?

These concepts will become important when I start building the AWS infrastructure with Terraform in Phase 5.

What I learned so far

The main thing I'm taking away from this phase is that the networking design needs to be understood before creating the infrastructure.

Terraform will eventually create many of these AWS resources for me, but I want to understand why those resources exist and how they connect together, not just copy Terraform code without knowing what it does.
