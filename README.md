Flask DevOps Project

An end-to-end DevOps project demonstrating how a small Flask application is packaged, deployed, and operated on AWS using industry-standard tooling.

The goal is not to build a complex app. The goal is to demonstrate the full path from git push to a running, monitored, autoscaling production service.

Project Status
Phase	Area	Status
1	Application (Flask)	✅ Complete
2	Git & GitHub workflow	✅ Complete
3	Docker	✅ Complete
4	AWS Networking (concepts)	⏳ In progress
5	Terraform	⬜ Not started
6	Terraform Modules & Remote State	⬜ Not started
7	AWS EKS	⬜ Not started
8	Kubernetes	⬜ Not started
9	Helm	⬜ Not started
10	AWS Load Balancer & Ingress	⬜ Not started
11	Amazon ECR	⬜ Not started
12	GitHub Actions CI/CD	⬜ Not started
13	Prometheus	⬜ Not started
14	Application Metrics	⬜ Not started
15	Grafana	⬜ Not started
16	Alerting	⬜ Not started
17	Security	⬜ Not started
18	Secrets Management	⬜ Not started
19	Reliability & Autoscaling	⬜ Not started
20	GitOps with Argo CD (optional)	⬜ Not started
21	Final End-to-End Testing	⬜ Not started
22	Documentation & Portfolio	⬜ Not started
Target Architecture
Developer
    |
    v
GitHub
    |
    v
GitHub Actions
    |
    +---- Test
    +---- Build
    +---- Scan
    |
    v
Amazon ECR
    |
    v
Amazon EKS
    |
    +-----------------------------+
    |             |               |
    v             v               v
  Pod           Pod             Pod
    |             |               |
    +-------------+---------------+
                  |
                  v
        AWS Application Load Balancer
                  |
                  v
                Users


Observability: Prometheus → Grafana → Alertmanager

Infrastructure: Terraform → VPC / EKS / ECR / IAM / Networking

Secrets: AWS Secrets Manager → Kubernetes → Application

Tech Stack
Application

Python 3.12

Flask 3

Gunicorn

prometheus-flask-exporter

python-json-logger

pytest

Containerization

Docker

Multi-layer caching

Non-root user

Health check

Base image: python:3.12-slim

Planned Infrastructure

Terraform — AWS infrastructure as code

Amazon EKS — managed Kubernetes

Kubernetes + Helm — orchestration and packaging

Amazon ECR — container registry

AWS Load Balancer Controller + Ingress — external access

GitHub Actions — CI/CD pipeline

Prometheus + Grafana + Alertmanager — observability

AWS Secrets Manager + External Secrets Operator — runtime secrets

Horizontal Pod Autoscaler — dynamic scaling

Application Endpoints
Endpoint	Purpose	Response
GET /	Landing page	{"message": "...", "version": "1.0.0"}
GET /health	Liveness / readiness probe target	{"status": "healthy"}
GET /version	Machine-readable version	{"version": "1.0.0"}
GET /metrics	Prometheus metrics	Prometheus exposition format
Running Locally
With Python

Create and activate a virtual environment:

python3 -m venv .venv
source .venv/bin/activate


Install dependencies:

pip install -r requirements-dev.txt


Run the tests:

pytest -v


Start the application:

flask --app app run


The application will be available at:

http://localhost:5000

With Docker

Build the Docker image:

docker build -t flask-app:1.0.0 .


Run the container:

docker run -d --name flask-test -p 5000:5000 flask-app:1.0.0


Check the health endpoint:

curl localhost:5000/health


View the container logs:

docker logs flask-test


Stop and remove the container:

docker stop flask-test
docker rm flask-test


The container runs as a non-root user (app) and includes a HEALTHCHECK that checks /health every 30 seconds.

Repository Layout
.
├── app.py                 # Flask application
├── test/
│   └── test_app.py        # pytest tests
├── requirements.txt       # production dependencies
├── requirements-dev.txt   # test/lint tooling
├── pyproject.toml         # pytest configuration
├── Dockerfile             # container image definition
├── .dockerignore          # files excluded from image build
├── .gitignore             # files excluded from Git
├── README.md              # project documentation
└── docs/
    ├── phases.md          # per-phase narrative
    └── troubleshooting.md # problems encountered and solutions

Workflow Conventions

This project follows a strict feature-branch workflow. Direct pushes to main are blocked by a GitHub Ruleset.

1. git checkout -b <type>/<short-description>
2. Make changes
3. Commit changes
4. git push -u origin <branch>
5. Open a Pull Request on GitHub
6. Merge once checks are green
7. Delete the branch


Commit message prefixes follow Conventional Commits:

feat: — a new feature

fix: — a bug fix

docs: — documentation only

chore: — tooling, configuration, or dependencies

Documentation

docs/phases.md — what each phase does and what was learned

docs/troubleshooting.md — real issues encountered during the build

Cost Awareness

This project builds real AWS infrastructure. During active work, resources should be destroyed when they are no longer needed.

For example:

terraform destroy


This helps prevent unnecessary AWS charges during development.

See docs/phases.md for cost notes on individual AWS resources.

Project Goal

The final goal is to demonstrate an end-to-end DevOps workflow:

Code
  |
  v
GitHub
  |
  v
GitHub Actions
  |
  v
Test / Build / Scan
  |
  v
Amazon ECR
  |
  v
Amazon EKS
  |
  v
Kubernetes
  |
  v
AWS Load Balancer
  |
  v
Users

Monitoring:
Prometheus → Grafana → Alertmanager


