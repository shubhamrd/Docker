## Practical Guide: Mastering Docker Environment Variables

**Objective:**

Learn how to manage configuration for Docker containers using environment variables, covering Dockerfile `ENV`, runtime `-e/--env`, and `--env-file` options.

### Basic Concepts

- **ENV (Dockerfile):** Sets default variables baked into the image.
- **-e / --env (CLI):** Overrides or sets variables at container runtime.
- **--env-file:** Loads multiple variables from a file.

### Scenario: Database‑Connected Web App

Deploy a **Node.js API** that connects to a **PostgreSQL** database. Provide the database URL and an API key without hard‑coding them.

#### Step 1: Using the Dockerfile (ENV)
```Dockerfile
# Sets the default environment to production
ENV NODE_ENV=production
# Sets a default port
ENV PORT=3000
```
These defaults are baked into the image layer.

#### Step 2: Running with CLI Flags (-e)
```bash
docker run -d --name my-api-cli \
  -e DB_URL=postgres://user:pass@db:5432/mydb \
  -e API_KEY=secret123 \
  my-node-app
```
- `-e` specifies a single key‑value pair.
- `--name` gives the container a recognizable name.
- Variables are injected directly into the container’s environment.

#### Step 3: Using an Environment File (`--env-file`)
Create a file `app1-nonprod.env`:
```
DB_URL=postgres://admin:password@localhost:5432/prod_db
API_KEY=987654321
DEBUG=false
```
Run the container:
```bash
docker run -d --name my-api-file \
  --env-file app1-nonprod.env \
  my-node-app
```
- `--env-file` reads each line and exports it as an environment variable.

#### Verification & Debugging
```bash
# Show all environment variables inside the running container
docker exec my-api-file env
```

### Precedence Rules (Highest → Lowest)
1. **docker run -e** (command‑line flags)
2. **--env-file** (variables from a file)
3. **Dockerfile ENV** (defaults baked into the image)

Use the highest‑precedence method for values that change per deployment, and Dockerfile `ENV` for sensible defaults.
