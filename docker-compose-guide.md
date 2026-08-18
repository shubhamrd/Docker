Docker Compose Quickstart
This tutorial introduces fundamental concepts of Docker Compose by guiding you through the development of a basic Python web application using the Flask framework and a Redis hit counter.

Prerequisites
Installed the latest version of Docker Compose

A basic understanding of Docker concepts

Step 1: Set up the project
Create a directory for the project:

Bash
$ mkdir compose-demo
$ cd compose-demo
Create app.py in your project directory and add the following:

Python
import os
import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(
    host=os.getenv("REDIS_HOST", "redis"),
    port=int(os.getenv("REDIS_PORT", "6379")),
)

@app.route("/")
def hello():
    count = cache.incr("hits")
    return f"Hello from Docker! I have been seen {count} time(s).\n"
Create requirements.txt in your project directory and add the following:

Plaintext
flask
redis
Create a Dockerfile:  

Dockerfile
# syntax=docker/dockerfile:1
FROM python:3.12-alpine
WORKDIR /code
ENV FLASK_APP=app.py
ENV FLASK_RUN_HOST=0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 5000
CMD ["flask", "run", "--debug"]
Create a .env file to hold configuration values:

Code snippet
APP_PORT=8000
REDIS_HOST=redis
REDIS_PORT=6379
Create a .dockerignore file to keep unnecessary files out of your build context:

Plaintext
.env
*.pyc
__pycache__
redis-data
Step 2: Define and start your services
Create compose.yaml in your project directory:

YAML
services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
  redis:
    image: redis:alpine
Start up your application:

Bash
$ docker compose up -d
Open http://localhost:8000. You should see the hit counter. Stop the stack before moving on:

Bash
$ docker compose down
Step 3: Fix the startup race with health checks
To ensure the web service waits for Redis to be fully ready, add a health check. Update compose.yaml:

YAML
services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
    depends_on:
      redis:
        condition: service_healthy
  redis:
    image: redis:alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s
Step 4: Enable Compose Watch for live updates
Because the Flask app is already running in --debug mode (which has a built-in auto-reloader), we can actually just use sync instead of sync+restart. This bypasses the restart bugs in older Docker Compose versions and is much faster.

Update your compose.yaml. Add the develop block to your web service. Your complete compose.yaml should look like this:

YAML
services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
      - FLASK_APP=app.py
      - FLASK_DEBUG=1
    volumes:
      - .:/code
    depends_on:
      redis:
        condition: service_healthy
    develop:
      watch:
        - action: sync
          path: .
          target: /code
        - action: rebuild
          path: requirements.txt

  redis:
    image: redis:alpine
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s
Start the stack in Watch Mode. Instead of the older docker compose watch command, you should launch the stack directly into watch mode using the --watch flag:

Bash
docker compose up --watch
Test the Live Update

Open your browser to http://localhost:8000.

Leave your terminal running. Open your app.py file in a code editor.

Change the greeting text (e.g., to return f"Hello from Compose Watch! I have been seen {count} time(s).\n").

Save the file.

Stop the stack before moving on:

Bash
$ docker compose down
Step 5: Persist data with named volumes
If you run docker compose down and then docker compose up, the Redis hit counter resets to zero. To preserve this data across container restarts, use a named volume.

Update compose.yaml to mount a volume for the Redis container:

YAML
services:
  web:
    # ... web configuration ...
  redis:
    image: redis:alpine
    volumes:
      - redis-data:/data
    healthcheck:
      # ... healthcheck configuration ...

volumes:
  redis-data:
Stop the stack and verify volume removal when necessary using:

Bash
$ docker compose down -v
Step 6: Structure your project with multiple Compose files
As applications grow, a single Compose file becomes harder to maintain. You can split services across multiple files and use the -f flag to combine them at runtime.

Create a new file called infra.yaml and move the Redis infrastructure into it:

infra.yaml

YAML
services:
  redis:
    image: redis:alpine
    volumes:
      - redis-data:/data
    healthcheck:
      test: ["CMD", "redis-cli", "ping"]
      interval: 5s
      timeout: 3s
      retries: 5
      start_period: 10s

volumes:
  redis-data:
Update your compose.yaml to focus on the web application:

YAML
services:
  web:
    build: .
    ports:
      - "${APP_PORT}:5000"
    environment:
      - REDIS_HOST=${REDIS_HOST}
      - REDIS_PORT=${REDIS_PORT}
    depends_on:
      redis:
        condition: service_healthy
    develop:
      watch:
        - action: sync+restart
          path: .
          target: /code
        - action: rebuild
          path: requirements.txt

  redis:
    image: redis:alpine
Start the application by passing both files. Compose will merge them at startup:  

Bash
docker compose -f compose.yaml -f infra.yaml up
Stop the stack before moving on:  

Bash
$ docker compose -f compose.yaml -f infra.yaml down
Step 7: Inspect and debug your running stack
With your stack running, you can observe logs and execute commands without stopping the containers.  

Stream logs from all services:

Bash
$ docker compose logs -f
Verify environment variables inside a running container:

Bash
$ docker compose exec web env | grep REDIS
Test internal networking:

Bash
$ docker compose exec web python -c "import redis; r = redis.Redis(host='redis'); print(r.ping())"
