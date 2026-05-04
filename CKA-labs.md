Docker Installation:

1\. Update System

**sudo yum -y remove buildah podman**

This command updates the system to ensure you have the latest packages
and dependencies.

2\. Install Required Dependencies

**sudo yum install -y yum-utils**

Install necessary packages for Docker.

3\. Add Docker Repository

**sudo yum-config-manager \--add-repo
https://download.docker.com/linux/centos/docker-ce.repo**

This adds the Docker repository to your system.

4\. Install Docker Engine

**sudo yum install -y docker-ce docker-ce-cli containerd.io
docker-buildx-plugin docker-compose-plugin**

Install Docker Engine using the added repository.

5\. Start and Enable Docker

**sudo systemctl start docker**

**sudo systemctl enable docker**

**sudo systemctl status docker**

Start and enable Docker to ensure it runs on system boot.

6\. Verify Docker Installation

**docker \--version**

Check the installed Docker version to confirm a successful installation.

Run hello world container

**docker run hello-world**

**docker ps**

**docker ps -a**

images pulling

**docker pull centos:8**

**docker images**

Create a centos container

1\. Pull a Base Image:

Choose a base image from Docker Hub.

**docker pull centos:8**

2\. Run a Container:

Start a basic container without specifying a command.

**docker run -it centos:8**

3\. Explore the Container:

Once inside the container, you can explore its filesystem or execute
commands.

**ls**

**cat /etc/os-release**

4\. Exit the Container:

To exit the container, type exit.

**exit**

5\. View Exited Containers:

Check the list of all containers, including the exited ones.

**docker ps**

**docker ps -a**

Copy container id

6\. Start the stopped container

**docker start container-id**

**docker ps**

7\. Connect to the container

**docker exec -it container-id bash**

**exit**

6\. Remove Containers

Remove the container.

**docker stop container-id**

**docker rm container-id**

**Create a ubuntu container container**.

1\. Pull a Base Image:

Choose a base image from Docker Hub.

**docker pull ubuntu**

2\. Run a Container:

Start a basic container without specifying a command.

**docker run -itd \--name ubuntu-container ubuntu**

**docker exec -it ubuntu-container bash**

3\. Explore the Container:

Once inside the container, you can explore its filesystem or execute
commands.

**ls**

**cat /etc/os-release**

4\. Exit the Container:

To exit the container, type exit.

**exit**

5\. View Exited Containers:

Check the list of all containers, including the exited ones.

**docker ps**

6\. stop and remove the container.

**docker stop container-id**

**docker rm container-id**

Practice: Hosting a Website with Apache HTTP Server Container

Objective:

To provide hands-on experience with Docker, hosting a website using the
Apache HTTP Server container, modifying the website content inside the
container, and utilizing port forwarding.

Practice Steps:

Step 1: Pull Apache HTTP Server Container

**docker pull public.ecr.aws/docker/library/httpd:latest**

Step 2: Run Apache HTTP Server Container

**docker run -d -p 8080:80 \--name my-apache-container
public.ecr.aws/docker/library/httpd:latest**

This command runs the Apache HTTP Server container in detached mode
(-d), maps host port 8080 to container port 80 (-p 8080:80), and assigns
a name to the container (\--name my-apache-container).

Note: check the used port number "ss -tulpen"

Step 3: Verify the Running Container

**docker ps**

Ensure the Apache HTTP Server container is running.

Step 4: Access the Website

Open a web browser and navigate to http://192.168.137.128:8080 to see
the default Apache welcome page.

Step 5: Get Inside the Container

docker exec -it my-apache-container /bin/bash

Access the container\'s shell interactively (-it).

Step 6: Change Index.html Content

echo \"\<h1\>Welcome to My Dockerized Website\</h1\>\" \>
/usr/local/apache2/htdocs/index.html

Modify the content of the index.html file with a unique message.

Step 7: Exit the Container

exit

Step 8: Refresh the Website

Visit http://192.168.137.128:8080 in your web browser again to see the
updated content.

 

**Practical Guide: Mastering Docker Environment Variables**

Environment variables are the industry-standard way to configure
applications without hardcoding values. This guide provides step-by-step
instructions for managing them in Docker, specifically tailored for
beginners.

**🛠 Basic Concepts**

- **ENV (Dockerfile):** Sets default variables within the image.

- **-e / \--env (CLI):** Overrides or sets variables at container
  runtime.

- **\--env-file:** Loads multiple variables from a file.

**🏗 Scenario: Database-Connected Web App**

Imagine you are deploying a **Node.js API** that needs to connect to a
**PostgreSQL database**. You must provide the database URL and an API
key without hardcoding them into your source code.

**Step 1: Using the Dockerfile (ENV)**

Use the ENV instruction to set \"sane defaults\" that should exist in
every container created from this image. These are hardcoded into the
image layer.

\# Sets the default environment to production

ENV NODE_ENV=production

\# Sets a default port

ENV PORT=3000

**Step 2: Running with CLI Flags (-e)**

When you run the container, you can inject or override variables. This
is the most common way to handle dynamic configuration.

**Command:** **docker run -d \--name my-api-cli -e
DB_URL=postgres://user:pass@db:5432/mydb -e API_KEY=secret123
my-node-app**

- **Explanation:**

  - **-e**: Specifies a single key-value pair.

  - **\--name my-api-cli**: We name this container my-api-cli to
    identify it.

  - **DB_URL=\...**: Injects the connection string directly into the
    container\'s shell environment.

**Step 3: Using an Environment File (app1-nonprod.env)**

For many variables, typing them in the CLI is tedious. Instead, we use
an external file.

**How to create the file using Nano:**

1.  Open the terminal and type: **nano app1-nonprod.env**

2.  Type or paste the following content into the editor:

3.  DB_URL=postgres://admin:password@localhost:5432/prod_db

4.  API_KEY=987654321

5.  DEBUG=false

6.  **To Save and Exit:**

    - Press **Ctrl + O** (This \"Writes Out\" the file).

    - Press **Enter** to confirm the filename.

    - Press **Ctrl + X** to exit the editor.

**Run the container using the file:**

**Command:** **docker run -d \--name my-api-file \--env-file
app1-nonprod.env my-node-app**

- **Explanation:**

  - **\--name my-api-file**: We use a different name here to avoid a
    \"Name Conflict\" error with the previous step.

  - **\--env-file**: Tells Docker to read the specified file
    (**app1-nonprod.env**) and export every line as an environment
    variable inside the container.

**🔍 Verification & Debugging**

To verify that your variables were successfully injected, use the exec
command to view the environment inside the running container.

**Command:** **docker exec my-api-file env**

**docker exec my-api-cli env**

- **Explanation:**

  - **env**: A standard Linux command that lists all active environment
    variables. Running it via docker exec shows exactly what the
    application sees.

**⚖️ Precedence Rules (Highest to Lowest)**

If you define the same variable in multiple places, Docker follows this
order:

1.  **docker run -e** (Command line flags override everything else).

2.  **\--env-file** (Variables loaded from an external file).

3.  **Dockerfile ENV** (The default values baked into the image).

**💡 Best Practices**

1.  **Never commit .env files** to Version Control (Git). Add them to
    .gitignore immediately to prevent leaking credentials.

2.  **Use .env.example** to show other developers which keys they need
    to define without sharing the actual values.

3.  **Avoid Secrets in ENV:** Environment variables can sometimes be
    leaked via logs or inspection. For highly sensitive data, use
    container secrets or a dedicated Vault.

🧹 Clean Up Steps

docker rm -f **my-api-file my-api-cli**

MySQL Container

Create container

docker run \--name db1 -d -e MYSQL_ROOT_PASSWORD=password123 -e
MYSQL_DATABASE=blankdb -e MYSQL_USER=shubham -e
MYSQL_PASSWORD=shubham123 public.ecr.aws/docker/library/mysql:latest

Verify the container is running

docker ps -a

Verify container logs

docker logs db1

connect to db option1

docker exec -it db1 bash

bash# mysql -u root -ppassword123

\> show databases;

\> create database newdb;

\> show databases;

\> exit

bash# exit

connect to db option2

docker exec -it db1 mysql -u root -ppassword123

\> exit

docker exec -it db1 mysql -u shubham -pshubham123

\> exit

 

**Practical Guide: Deploying WordPress and MySQL Pods**

This guide walks you through setting up a WordPress website connected to
a MySQL database using Docker containers on the **default network**. We
will focus on how these two services communicate using environment
variables and professional naming conventions.

**🛠 Basic Concepts**

- **Default Network:** By default, Docker places containers on a
  \"bridge\" network.

- **IP Connectivity:** On the default network, containers must
  communicate using their IP addresses rather than their names.

**🏗 Scenario: Launching a WordPress Site (Non-Prod)**

You need to launch a WordPress container and a MySQL container for a
testing environment using the default Docker settings.

**Step 1: Configure and Run the MySQL Database**

We will use an environment file to keep our database credentials
organized and separate from our commands.

**Create the Database Config File:**

1.  Open the terminal and type: **nano nonprod-mysql-db.env**

2.  Paste the following content:

> MYSQL_ROOT_PASSWORD=nonprod_root_secure_pass
>
> MYSQL_DATABASE=nonprod_wordpress_db
>
> MYSQL_USER=nonprod_wp_user
>
> MYSQL_PASSWORD=nonprod_wp_pass

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Launch the MySQL Container:**

**Command:** **docker run -d \--name nonprod-mysql-db \--env-file
nonprod-mysql-db.env mysql:8.0**

- **Explanation:**

  - **\--name nonprod-mysql-db**: This is the name of our database
    container. Since we are on the default network, we will need to find
    this container\'s IP address in the next step.

**Step 2: Identify the Database IP Address**

On the default network, WordPress cannot find the database by the name
nonprod-mysql-db. You must find its internal IP address.

**Command:** **docker inspect -f \'{{range
.NetworkSettings.Networks}}{{.IPAddress}}{{end}}\' nonprod-mysql-db**

- **Explanation:** This command extracts the internal IP address (e.g.,
  172.17.0.2) assigned to your MySQL container. **Make a note of this
  IP.**

**Step 3: Configure and Run WordPress**

Now we tell WordPress how to find the database using the IP address you
just found.

**Create the WordPress Config File:**

1.  Open the terminal and type: **nano nonprod-wp-app.env**

2.  Paste the following content (Replace 172.17.0.X with the IP from
    Step 2):

> WORDPRESS_DB_HOST=172.17.0.X
>
> WORDPRESS_DB_USER=nonprod_wp_user
>
> WORDPRESS_DB_PASSWORD=nonprod_wp_pass
>
> WORDPRESS_DB_NAME=nonprod_wordpress_db

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Launch the WordPress Container:**

**Command:** **docker run -d \--name nonprod-wordpress-app -p 8080:80
\--env-file nonprod-wp-app.env wordpress:latest**

- **Explanation:**

  - **-p 8080:80**: Maps port 8080 on your machine to the container.

  - **WORDPRESS_DB_HOST**: Uses the specific IP address of the database
    container.

**🔍 Verification & Connectivity Test**

**1. Check if Containers are Running**

**Command:** **docker ps**

**2. Verify Database Connection**

Visit http://\<node1IP\>:8080 in your browser. If you see the WordPress
setup screen, the containers are communicating correctly!

**3. Inspect Logs if Connectivity Fails**

**Command:** **docker logs nonprod-wordpress-app**

**🧹 Clean Up Steps**

Follow these steps to remove the practice resources and keep your
environment clean.

**1. Stop and Remove Containers**

**Command:** **docker rm -f nonprod-wordpress-app nonprod-mysql-db**

**2. Delete Environment Files**

**Command:** **rm nonprod-mysql-db.env nonprod-wp-app.env**

**💡 Best Practices**

1.  **Custom Networks vs Default:** In production, always use custom
    networks (docker network create) because they allow containers to
    talk to each other by **name** instead of unstable IP addresses.

2.  **Environment Separation:** Always use prefixes like nonprod- or
    prod- to avoid accidentally deleting the wrong resource.

3.  **Volume Persistence:** In a real setup, add -v
    nonprod_mysql_data:/var/lib/mysql to the MySQL command so your data
    is saved permanently.

**Practice Guide: Docker Commit and Push to Docker Hub**

Objective:

Demonstrate the use of the docker commit command to create a new Docker
image and push it to Docker Hub.

Practice Steps:

Step 1: Sign Up for Docker Hub

Visit Docker Hub and sign up for a new account.

https://hub.docker.com/

Step 2: Create a public Repository

Log in to Docker Hub.

In the top navigation, click on \"Repositories\" and then \"Create
Repository.\"

Provide a name for the repository (e.g., my-public-repo) and set it to
public. Click \"Create.\"

Step 3: Get User Access Keys

In the Docker Hub dashboard, go to \"Account Settings\" \> \"personal
access tokens\" \> \"New Access Token.\"

Generate a new access token, providing the necessary permissions (Read &
Write).

Step 4: Connect to Docker Hub

In the terminal, log in to Docker Hub using the Docker CLI.

**docker login -u YOUR_DOCKER_HUB_USERNAME -p YOUR_ACCESS_TOKEN**

Step 5: Create a Container

Launch a new container from a base image.

**docker run -itd \--name my-container ubuntu:latest**

Step 6: Connect to the Container

**docker exec -it my-container /bin/bash**

Step 7: Make Changes in the Container

Inside the container, install a text editor (e.g., nano).

Create a sample file using the text editor.

**echo " Enter some content and save the file." \> sample.txt**

**cat sample.txt**

**touch app.config app.data app.code**

**ls**

Step 8: Commit Changes to a New Image

Exit the container.

**exit**

Use the docker commit command to create a new image.

**docker commit my-container
YOUR_DOCKER_HUB_USERNAME/my-custom-image:latest**

Step 9: Push the Image to Docker Hub

Push the newly created image to Docker Hub.

**docker push YOUR_DOCKER_HUB_USERNAME/my-custom-image:latest**

Step 10: Verify on Docker Hub

Stop and remove the currently running container.

Get the new image path, uploaded your repository.

Create container with same image.

Connect to container and check for same file.

Verify Optional:

1.  open and login <https://labs.play-with-docker.com/>

2.  create instance

3.  pull image

docker pull reponame:tag

create container, get in and check the file

**Dockerfile**

https://docs.docker.com/reference/dockerfile/

This guide walks you through creating a directory, setting up a
Dockerfile, building images, and managing container versions.

**1. Setup Workspace**

Create a directory to keep your project files organized and move into
it.

mkdir data cd data

**2. Create the Dockerfile**

Open the text editor to create the configuration file.

nano Dockerfile

**Copy and paste the following content into the editor:**

FROM nginx

WORKDIR /usr/share/nginx/html

COPY index.html .

RUN mkdir dummydata

RUN touch dummydata/dummyfile

ADD
https://www.docker.com/app/uploads/2021/11/container-what-is-container-1110x961.png
.

**Save and Exit Nano:**

1.  Press Ctrl+O then Enter (to save)

2.  Press Ctrl+X (to exit)

**Verify the file was created correctly:**

cat Dockerfile

**3. Create Initial Content**

Create a simple HTML file that will be copied into your image.

echo \"file created using dockerfile\" \> index.html ls

**4. Build and Run Version 1**

Build your first Docker image tagged as version 1.

docker build -t app2:v1 .

**Check that the image exists:**

docker images

**Run the container in the background (detached mode):**

docker run \--name app4 -d app2:v1

**Verify the container is running:**

docker ps

**5. Inspect Container Internals**

Log into the running container to check the file system.

docker exec -it app4 bash

**Once inside the container, run these commands:**

ls cat index.html

exit

**6. Create Version 2**

Modify the HTML file to create a new version of your application.

echo \"version2\" \>\> index.html

**Build the new image tagged as version 2:**

docker build -t app2:v2 .

**Verify both versions exist:**

docker images

**7. Run Version 2**

Start a new container using the updated image. Note that we give it a
different name (app5).

docker run \--name app5 -d app2:v2

**8. Verification via IP**

Find the internal IP address of the new container and test the
connection.

docker inspect app5

*(Scroll through the output to find \"IPAddress\" and copy it)*

**Test the endpoint (replace \<container-ip\> with the actual IP):**

curl \<container-ip\>

**\**

**Practice2: Ubuntu Shell Script Application**

This section covers creating a custom shell script, using a specific
Dockerfile name, and checking container logs.

**1. Create the Shell Script**

Create a script that prints a message repeatedly.

nano script1.sh

**Paste the following content:**

while true do echo \"the script is running\" sleep 5 done

**Save and Exit:**

1.  Press Ctrl+O then Enter

2.  Press Ctrl+X

**Verify the file exists:**

ls

**2. Create a Custom Dockerfile**

Create a Dockerfile with a specific name (dockerfile1) instead of the
default.

nano dockerfile1

**Paste the following content:**

FROM ubuntu

WORKDIR /mnt

COPY \*.sh .

CMD \[\"sh\", \"script1.sh\"\]

**Save and Exit:**

1.  Press Ctrl+O then Enter

2.  Press Ctrl+X

**Verify the file content:**

cat dockerfile1

**3. Build with Custom File**

Build the image using the -f flag to point to your custom Dockerfile.

docker build -t bashapp:v1 -f dockerfile1 .

**Check the image:**

docker images

**4. Run and Monitor**

Run the container in detached mode.

docker run \--name bashapp -d bashapp:v1

**Check the container status (it should be Up):**

docker ps -a

**View the logs to verify the script is running:**

docker logs bashapp

Practice3: **Entrypoint and Environment Scripts**

This section demonstrates how to use ENTRYPOINT combined with CMD to
create flexible containers that can run different scripts (Prod vs
Non-Prod) based on command-line arguments.

**1. Create Environment Scripts**

Create two separate scripts to simulate different environments.

**Create the Non-Prod script:**

nano nonprod.sh

**Paste the following content:**

while true

do echo \"This is non prod environment\"

sleep 5

done

**Save and Exit (Ctrl+O, Enter, Ctrl+X).**

**Create the Prod script:**

nano prod.sh

**Paste the following content:**

while true

do echo \"This is prod environment\"

sleep 5

done

**Save and Exit.**

**2. Update the Dockerfile**

Modify dockerfile1 to use ENTRYPOINT. This sets the executable to sh,
while CMD provides the default argument (nonprod.sh).

nano dockerfile1

**Replace the content with:**

FROM ubuntu

WORKDIR /mnt

COPY \*.sh .

ENTRYPOINT \[\"sh\"\]

CMD \[\"nonprod.sh\"\]

**Save and Exit.**

**3. Build Version 3**

Build the new image version using the updated Dockerfile.

docker build -t bashapp:v3 -f dockerfile1 .

**4. Run Default (Non-Prod)**

Run the container without extra arguments. It will use the default CMD
(nonprod.sh).

docker run \--name nonprodapp3 -d bashapp:v3

**Check status and logs:**

docker ps

docker logs nonprodapp3

*(Output should say \"This is non prod environment\")*

**5. Run Override (Prod)**

Run the container and explicitly provide prod.sh as an argument. This
overrides the default CMD.

docker run \--name prodapp3 -d bashapp:v3 prod.sh

**Check status and logs:**

docker ps

docker logs prodapp3

*(Output should say \"This is prod environment\")*

**Dockerfile workshop:
https://docs.docker.com/get-started/workshop/02_our_app/**

nano docker-compose.yaml

name: web-compose

services:

web:

image: nginx

ports:

\- \"8090:80\"

\- \"8443:443\"

\- \"8080:8090\"

environment:

\- NGINX_HOST=localhost

\- NGINX_PORT=80

volumes:

\- nginx:/etc/nginx/

db:

image: mysql

environment:

\- MYSQL_ROOT_PASSWORD=example

ports:

\- \"3306:3306\"

volumes:

nginx-volume:

db-volume:

Save and exit

docker compose up -d

docker ps -a

docker compose ls

docker compose ps

docker compose logs

docker compose top

docker compose down

docker compose ls

docker ps -a

**wordpress with docker compose**

create a dir with name wp-compose

mkdir wp-compose

cd wp-compose

wget
https://github.com/docker/awesome-compose/blob/master/wordpress-mysql/compose.yaml

ls

cat compose.yaml

docker compose up -d

docker ps

Official repo: <https://github.com/docker/awesome-compose/tree/master>

Compose workshop: <https://docs.docker.com/compose/gettingstarted/>

Docker volumes: <https://docs.docker.com/engine/storage/volumes/>

**\**

**Practical Guide: Mastering Docker Compose**

Docker Compose is a tool for defining and running multi-container
applications. Instead of running multiple docker run commands, you use a
single YAML file to configure all your services.

**🛠 Basic Concepts**

- **docker-compose.yml**: The configuration file where you define your
  services, networks, and volumes.

- **Services**: Different parts of your app (e.g., a database and a web
  server).

- **Orchestration**: Managing the lifecycle (start, stop, build) of
  multiple containers as a single unit.

**🏗 Scenario: Non-Prod Multi-Tier Application**

We will deploy a **WordPress** site and a **MySQL** database. Unlike
manual docker run commands, Docker Compose automatically creates a
network so containers can talk to each other by their service names.

**Step 1: Create the Project Directory**

It is best practice to keep every Compose project in its own folder.

**Command:** **mkdir nonprod-wp-project && cd nonprod-wp-project**

- **Explanation:** This creates a folder named nonprod-wp-project and
  moves you into it.

**Step 2: Create the Environment File**

We will store our sensitive credentials in a .env file. Docker Compose
reads this file automatically.

1.  Open the terminal and type: **nano .env**

2.  Paste the following content:

> \# Database Settings
>
> MYSQL_ROOT_PASSWORD=nonprod_root_secure_123
>
> MYSQL_USER=nonprod_wp_user
>
> MYSQL_PASSWORD=nonprod_wp_pass
>
> MYSQL_DATABASE=nonprod_wordpress_db
>
> \# WordPress Settings
>
> WP_PORT=8080

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Step 3: Create the Docker Compose File**

This file defines our services and how they interact.

1.  Open the terminal and type: **nano docker-compose.yml**

2.  Paste the following content:

> services:
>
> nonprod-db:
>
> image: mysql:8.0
>
> restart: always
>
> environment:
>
> MYSQL_ROOT_PASSWORD: \${MYSQL_ROOT_PASSWORD}
>
> MYSQL_DATABASE: \${MYSQL_DATABASE}
>
> MYSQL_USER: \${MYSQL_USER}
>
> MYSQL_PASSWORD: \${MYSQL_PASSWORD}
>
> volumes:
>
> \- db_data:/var/lib/mysql
>
> nonprod-wordpress:
>
> depends_on:
>
> \- nonprod-db
>
> image: wordpress:latest
>
> restart: always
>
> ports:
>
> \- \"\${WP_PORT}:80\"
>
> environment:
>
> WORDPRESS_DB_HOST: nonprod-db
>
> WORDPRESS_DB_USER: \${MYSQL_USER}
>
> WORDPRESS_DB_PASSWORD: \${MYSQL_PASSWORD}
>
> WORDPRESS_DB_NAME: \${MYSQL_DATABASE}
>
> volumes:
>
> db_data:

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Step 4: Launch the Application**

Now, we start both containers with a single command.

**Command:** **docker compose up -d**

- **Explanation:**

  - **up**: Tells Compose to create and start the containers, networks,
    and volumes defined in the YAML file.

  - **-d**: Runs the containers in \"detached\" mode (in the
    background).

**🔍 Verification & Management**

**1. Check Service Status**

**Command:** **docker compose ps**

- **Explanation:** Shows the status of all services managed by this
  specific Compose file.

**2. View Real-time Logs**

**Command:** **docker compose logs -f**

- **Explanation:** Streams the logs from all containers. Press **Ctrl +
  C** to stop viewing.

**3. Verify the Website**

Visit http://localhost:8080 (or your Node IP). If you see the WordPress
installation screen, the connection to the database was successful.

**🧹 Clean Up Steps**

Docker Compose makes cleaning up extremely easy compared to manual
commands.

**1. Stop and Remove Resources**

**Command:** **docker compose down**

- **Explanation:** Stops and removes the containers and the internal
  network created by Compose.

**2. Remove Volumes (Optional)**

If you want to delete the database data as well: **Command:** **docker
compose down -v**

- **Explanation:** The **-v** flag tells Compose to also remove the
  named volumes (permanently deleting your database data).

**3. Delete Project Files**

**Command:** **cd .. && rm -rf nonprod-wp-project**

**💡 Best Practices**

1.  **Service Naming:** Use service names (like nonprod-db) in your
    application configs. Compose acts as a DNS server so containers find
    each other automatically.

2.  **Environment Variables:** Use a .env file for secrets. Never
    hardcode passwords directly into the docker-compose.yml.

3.  **Depends On:** Use depends_on to ensure the database starts before
    the web application.

**Practice Scenario: Docker Bind Mount with Index.html**

In this updated practice, students will work with a Docker bind mount to
share an index.html webpage between the host and the container. Sample
website data will be used for demonstration.

Practice Overview:

Bind Mount Practice with Index.html:

Scenario: Students will create an Nginx container, bind-mounting a local
directory containing an index.html file with sample website data.

Step-by-Step Guide:

1\. Bind Mount Practice with Index.html:

Create Web Content Directory:

mkdir /web-content

echo "some content" \> /web-content/index.html

Run Nginx Container with Bind Mount:

docker run -d \--name nginx-container -v
/web-content:/usr/share/nginx/html -p 8080:80 nginx:latest

Verify Sample Website:

Open http://nodeip:8080 in a web browser.

Update Content Locally:

Make changes to /web-content/index.html locally.

echo "version 2" \>\> /web-content/index.html

Verify Updated Content in Container:

Refresh the web browser at http://nodeip:8080 to see the changes
reflected.

Verify Container Data Locally:

Check the modified /web-content/index.html file on the local machine to
ensure changes persist.

**MySQL Database Persistence with Docker Volumes**

To ensure MySQL database persistence using Docker volumes, follow this
revised scenario. It includes creating a MySQL container with a volume,
connecting to it, creating a sample database, exiting the container,
launching another MySQL container with the same volume, and verifying
the presence of the database in the newly launched container.

1\. Scenario Overview:

2\. Create MySQL Container with Volume: Create a Docker container with
MySQL, utilizing a Docker volume for data persistence.

3\. Connect and Access MySQL Database: Connect to the MySQL container,
access the MySQL database inside, and create a sample database.

4\. Exit Container: Exit the MySQL container to simulate a shutdown or
container termination.

5\. Launch Another MySQL Container: Launch a new MySQL container with
the same volume.

6\. Verify Database Persistence: Verify that the sample database created
earlier is present in the newly launched MySQL container.

Step-by-Step Guide:

1\. Create MySQL Container with Volume:

docker run -d \--name mysql-container -v mysql-data:/var/lib/mysql -e
MYSQL_ROOT_PASSWORD=password mysql:latest

2\. Connect and Access MySQL Database:

docker exec -it mysql-container mysql -uroot -p

Inside MySQL:

CREATE DATABASE sample_db;

show databases;

exit;

3\. Exit Container:

exit

docker stop mysql-container

4\. Launch Another MySQL Container:

docker run -d \--name mysql-container-2 -v mysql-data:/var/lib/mysql -e
MYSQL_ROOT_PASSWORD=password mysql:latest

5\. Login to second container database and list database.

docker exec -it mysql-container-2 mysql -uroot -p

Inside MySQL:

show databases;

6\. Clean up. Stop and remove both containers.

docker stop mysql-container-2 mysql-container

docker rm mysql-container-2 mysql-container

docker volume rm mysql-data

**Network Practice**

Docker Networking Practice Guide

Objective:

Demonstrate Docker networking with real-time examples using a custom
network and connecting containers within that network.

Example: Docker Networking

Step 1: Create a Custom Bridge Network

\# Create a custom bridge network

docker network create mynetwork

Step 2: Run Containers in the Custom Network

\# Run Nginx container in the custom network

docker run -d \--name nginx-container \--network mynetwork nginx:alpine

\# Run Alpine container in the same network

docker run -it \--name alpine-container \--network mynetwork alpine sh

Step 3: Check Connectivity Between Containers

Inside the Alpine container, check if it can reach the Nginx container:

ping nginx-container

exit

Step 4: Expose Ports Between Containers

\# Stop and remove existing containers

docker stop nginx-container alpine-container

docker rm nginx-container alpine-container

\# Run Nginx container exposing port 80

docker run -d \--name nginx-container \--network mynetwork -p 8080:80
nginx:alpine

\# Run Alpine container in the same network

docker run -it \--name alpine-container \--network mynetwork alpine sh

Inside the Alpine container, check if it can access Nginx:

\# From within the Alpine container

wget -qO- nginx-container:80

Cleanup:

\# Stop and remove containers

docker stop nginx-container alpine-container

docker rm nginx-container alpine-container

\# Remove the custom network

docker network rm mynetwork

**Host Network**

docker run \--name host-con -d \--network host nginx

docker ps

docker inspect host-con

curl localhost:80

Open browser with node1ip:80 . website will not work

firewall-cmd \--add-port=80/tcp

Open browser with node1ip:80

**None Network**

docker run \--name none-con -d \--network none nginx

docker ps

docker inspect none-con

**Practical Guide: Basic Pod Management (CLI)**

This guide focuses on managing Pods directly via the command line (CLI).
Using the CLI is essential for quick troubleshooting, labeling, and
administrative tasks in a containerized environment.

**🛠 Basic Concepts**

- **Pod**: The smallest deployable unit in Kubernetes/container
  orchestration, representing a single instance of a running process.

- **Labels**: Key-value pairs attached to Pods used for organizing and
  filtering.

- **CLI Management**: Performing actions directly via commands rather
  than configuration files (YAML).

**🏗 Scenario: Non-Prod Application Management**

You are tasked with managing several application pods in a testing
environment (nonprod). You will create, label, and filter these pods to
simulate a real-world workflow.

**Step 1: Create a Pod**

We will create a simple Nginx pod to represent a web server.

**Command:** **kubectl run nonprod-webapp \--image=nginx**

- **Explanation:**

  - **run**: Creates a new Pod.

  - **nonprod-webapp**: The name of the Pod.

  - **\--image=nginx**: Specifies the container image to use.

**Step 2: List Pods**

To see if your Pod was created successfully.

**Command:** **kubectl get pods**

- **Explanation:** Lists all Pods in the current namespace and shows
  their status (e.g., Running, Pending).

**Step 3: Describe a Pod (Detailed Info)**

If a Pod is stuck in \"Pending\" or \"CrashLoopBackOff,\" you need to
see the detailed status and events.W

**Command:** **kubectl describe pod nonprod-webapp**

- **Explanation:**

  - **describe**: Shows detailed information about the resource,
    including its IP, labels, container status, and a list of recent
    **Events** (e.g., \"Successfully pulled image\").

**Step 4: View Pod Logs**

Checking logs is the first step in debugging an application.

**Command:** **kubectl logs nonprod-webapp**

- **Explanation:** Displays the standard output (STDOUT) of the
  container running inside the Pod.

**Step 5: Execute Commands Inside a Pod**

Sometimes you need to enter the Pod to check files or configurations.

**Command:** **kubectl exec -it nonprod-webapp \-- /bin/bash**

- **Explanation:**

  - **-it**: Interactive terminal mode.

  - **\-- /bin/bash**: The command to run inside the container to give
    you a shell prompt.

  - run command **cat /etc/os-release**

  - **To Exit:** Type **exit** and press **Enter**.

**Step 6: Labeling and Overriding**

Labels allow you to categorize Pods (e.g., by environment or tier).

**Adding a Label:**

**Command:** **kubectl label pods nonprod-webapp env=nonprod
tier=frontend**

- **Explanation:** Adds two labels (env and tier) to the nonprod-webapp
  pod.

**Overriding an Existing Label:**

**Command:** **kubectl label pods nonprod-webapp tier=web-server
\--overwrite**

- **Explanation:**

  - **\--overwrite**: Required if the label key (tier) already exists
    and you want to change its value.

**Step 7: Filtering Pods Based on Labels**

When you have hundreds of pods, you use filters (selectors) to find
specific ones.

**Command:** **kubectl get pods -l env=nonprod**

- **Explanation:**

  - **-l**: Stands for \"selector\" or \"label\". This command only
    shows pods that have the env=nonprod label.

**Step 8: Removing a Label (De-labeling)**

If a label is no longer needed, you can remove it.

**Command:** **kubectl label pods nonprod-webapp tier-**

- **Explanation:** Adding a dash (**-**) immediately after the label key
  name removes that label from the Pod.

**🧹 Clean Up Steps**

Always remove your practice resources to keep the environment clean.

**1. Delete the Pod**

**Command:** **kubectl delete pod nonprod-webapp**

- **Explanation:** Stops the container and removes the Pod definition
  from the system.

**💡 Best Practices**

1.  **Descriptive Names**: Always include the environment prefix (e.g.,
    nonprod-) in the Pod name.

2.  **Standard Labels**: Establish a standard set of labels (e.g., app,
    env, owner) for all Pods.

3.  **Describe First for Infrastructure Errors**: If the pod won\'t
    start, use describe. If the pod starts but the app crashes, use
    logs.

Open VSCode

Create a YAML file

\$ Kubectl explain pods

\$ Kubectl explain pods \--recursive

\$ kubectl api-resources

\$ kubectl run firstpod1 \--image nginx \--dry-run -o yaml

![](media/image1.png){width="1.8083333333333333in" height="2.075in"}

\$ kubectl run firstpod1 \--image nginx \--dry-run -o yaml \> newpod.yml

edit newpod.yml

apiVersion: v1

kind: Pod

metadata:

labels:

run: firstpod1

env: test

name: firstpod2

spec:

containers:

\- image: httpd

name: firstpod1

kubectl create -f newpod.yml \--dry-run

kubectl create -f newpod.yml

kubectl get pods

kubectl describe pod firstpod

edit newpod.yml and add label

kubectl diff -f flle_name

kubectl apply -f newpod.yml

kubectl get pod firstpod2 \--show-labels

kubectl edit pod firstpod2

kubectl get pod firstpod2 \--show-labels

kubectl delete -f newpod.yml

kubectl get pods

**Practical Guide: Declarative Pod Management (YAML & Inspection)**

This guide covers how to manage Kubernetes resources using YAML files
(declarative approach) and how to use inspection tools to understand
resource structures.

**🛠 Basic Concepts**

- **Declarative Management**: Using files (YAML) to define the \"desired
  state\" of your cluster.

- **Dry Run**: Simulating a command to see the output or generate a
  template without actually creating a resource.

- **API Exploration**: Using tools to see what fields are available in a
  Kubernetes resource.

**🏗 Scenario: Non-Prod Resource Exploration and Deployment**

You are moving beyond simple CLI commands to managing resources using
configuration files, which is the standard for production environments.

**Step 1: Explore API Resources**

Before creating a YAML file, you need to know what fields are available
and what resources the cluster supports.

**Command:** **kubectl api-resources**

- **Explanation:** Lists all available resource types (Pods, Services,
  etc.) supported by your cluster.

**Command:** **kubectl explain pods**

- **Explanation:** Provides documentation for the Pod resource. It shows
  the top-level fields like apiVersion, kind, metadata, and spec.

**Command:** **kubectl explain pods \--recursive**

- **Explanation:** Shows the entire tree of fields available within a
  Pod, helping you find deeply nested configuration options.

**Step 2: Generate a YAML Template**

Instead of writing YAML from scratch, use a dry-run to generate a
template.

**Command:** **kubectl run firstpod1 \--image=nginx \--dry-run=client -o
yaml \> newpod.yml**

- **Explanation:**

  - **\--dry-run=client**: Tells Kubernetes NOT to create the pod, just
    simulate the request.

  - **-o yaml**: Outputs the result in YAML format.

  - **\> newpod.yml**: Redirects the output into a new file named
    newpod.yml.

**Step 3: Edit the YAML File**

Open the file to modify it for your specific needs.

1.  Open the file: **nano newpod.yml**

2.  Modify the content to match this configuration:

> apiVersion: v1
>
> kind: Pod
>
> metadata:
>
> labels:
>
> run: firstpod1
>
> env: test
>
> name: firstpod2
>
> spec:
>
> containers:
>
> \- image: httpd
>
> name: firstpod1

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Step 4: Validate and Create the Pod**

Always validate your YAML before applying it to the cluster.

**Command:** **kubectl create -f newpod.yml \--dry-run=client**

- **Explanation:** Validates that the YAML syntax is correct without
  creating the resource.

**Command:** **kubectl create -f newpod.yml**

- **Explanation:** Creates the Pod defined in the file.

**Step 5: Verify and Inspect**

**Command:** **kubectl get pods**

**Command:** **kubectl describe pod firstpod2**

- **Explanation:** Use describe to check if the Pod started correctly or
  to see if there are any errors.

**Step 6: The \"Apply\" Workflow (Updates)**

In professional environments, we use apply to update resources.

1.  Edit the file to add a new label: **nano newpod.yml**

2.  Under labels:, add **team: dev**. Save and exit.

**Command:** **kubectl diff -f newpod.yml**

- **Explanation:** Shows the difference between your local file and the
  live resource in the cluster (like a \"git diff\").

**Command:** **kubectl apply -f newpod.yml**

- **Explanation:** Updates the live Pod with the changes made in your
  YAML file.

**Command:** **kubectl get pod firstpod2 \--show-labels**

**Step 7: Direct Live Editing**

Sometimes you need to fix a live resource without changing the local
file.

**Command:** **kubectl edit pod firstpod2**

- **Explanation:** Opens the live configuration in your default editor.
  After you save and exit, Kubernetes applies the changes immediately.

**Command:** **kubectl get pod firstpod2 \--show-labels**

**🧹 Clean Up Steps**

**Command:** **kubectl delete -f newpod.yml**

- **Explanation:** Deletes the resource defined in the file.

**Command:** **rm newpod.yml**

**💡 Best Practices**

1.  **Dry-Run Often**: Use \--dry-run=client -o yaml to generate perfect
    templates every time.

2.  **Apply over Create**: Use kubectl apply for most tasks as it
    handles updates gracefully.

3.  **Check Labels**: Use \--show-labels frequently to ensure your
    organization logic is correct.

**Practical Guide: Managing StatefulSets (Database Scenario)**

StatefulSets are the standard way to run stateful applications in
Kubernetes, such as databases (MySQL, MongoDB) or distributed systems.
Unlike Deployments, Pods in a StatefulSet have a stable identity and
their own dedicated storage.

**🛠 Basic Concepts**

- **Stable Network Identity:** Pods are named with an ordinal index
  (e.g., mysql-0, mysql-1) that never changes.

- **Persistent Storage:** Each Pod is automatically mapped to its own
  Persistent Volume (PV). If mysql-0 crashes, it restarts and
  re-attaches to the exact same disk.

- **Ordered Operations:** Pods are created, updated, and deleted in a
  strict, predictable order.

**🏗 Real-Time Use Case: Non-Prod Database Cluster**

You are setting up a MySQL cluster for a \"Non-Prod\" environment. The
requirement is that even if a Pod is deleted or moved to another
physical server, the data must remain intact and the Pod name must stay
the same so the application doesn\'t lose its connection.

**Step 1: Define the StatefulSet YAML**

We will create a YAML file that defines a 3-node MySQL cluster with
**Volume Claim Templates** for persistent storage.

1.  Create and open a new file: **nano nonprod-mysql-ss.yml**

2.  Paste the following configuration:

apiVersion: apps/v1

kind: StatefulSet

metadata:

name: nonprod-mysql

labels:

app: mysql

env: nonprod

spec:

serviceName: \"mysql-h\"

replicas: 3

selector:

matchLabels:

app: mysql

template:

metadata:

labels:

app: mysql

spec:

containers:

\- name: mysql

image: mysql:8.0

env:

\- name: MYSQL_ROOT_PASSWORD

value: \"nonprod_password\"

ports:

\- containerPort: 3306

name: mysql

volumeMounts:

\- name: data

mountPath: /var/lib/mysql

volumeClaimTemplates:

\- metadata:

name: data

spec:

accessModes: \[ \"ReadWriteOnce\" \]

resources:

requests:

storage: 1Gi

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Step 2: Deploy the StatefulSet**

**Command:** **kubectl apply -f nonprod-mysql-ss.yml**

- **Explanation:** This starts the StatefulSet controller. Because we
  included volumeClaimTemplates, Kubernetes will automatically provision
  three separate 1Gi disks (PVCs), one for each pod.

**Step 3: Verify Ordered Deployment**

**Command:** **kubectl get pods -w**

- **Explanation:** Observe the output. You will notice that
  nonprod-mysql-1 does not start until nonprod-mysql-0 is fully
  **Running**. This ordered startup is critical for database clusters.
  Press **Ctrl + C** to exit.

**Step 4: Testing Network Identity**

**Command (Standard):** **kubectl exec -it nonprod-mysql-0 \-- cat
/etc/hostname**

**Command (Windows/Git Bash Fix):** **kubectl exec -it nonprod-mysql-0
\-- cat //etc/hostname**

- **Explanation:** Unlike Deployments, StatefulSet Pods use their name
  as their hostname.

- **Troubleshooting (Windows):** If you are using Git Bash, you must use
  cat //etc/hostname. The double slash tells the terminal not to convert
  the path to a Windows path, while cat remains the necessary command to
  read the file.

**Step 5: Testing Data Persistence (The Real Test)**

We will create data in the first pod, delete the pod, and verify that
the data survived because it was stored on a Persistent Volume.

1.  **Create a test database inside the pod:** **kubectl exec -it
    nonprod-mysql-0 \-- mysql -u root -pnonprod_password -e \"CREATE
    DATABASE prod_test_db;\"**

2.  **Delete the pod to simulate a failure:** **kubectl delete pod
    nonprod-mysql-0**

3.  **Verify recovery:** Wait for the pod to reach \"Running\" status
    again, then check the databases: **kubectl exec -it nonprod-mysql-0
    \-- mysql -u root -pnonprod_password -e \"SHOW DATABASES;\"**

- **Explanation:** You should see prod_test_db in the list. The new pod
  instance automatically re-attached to the existing disk containing
  your data.

**Step 6: Scaling the Cluster**

**Command:** **kubectl scale statefulset nonprod-mysql \--replicas=5**

- **Explanation:** Kubernetes will add pods nonprod-mysql-3 and
  nonprod-mysql-4. Each will get its own new 1Gi disk automatically.

**🔍 Inspection & Troubleshooting**

**Command:** **kubectl get pvc**

- **Explanation:** This shows the Persistent Volume Claims. You will see
  one for each pod (e.g., data-nonprod-mysql-0).

**Command:** **kubectl describe statefulset nonprod-mysql**

**🧹 Clean Up Steps**

**Command:** **kubectl delete statefulset nonprod-mysql**

**Command:** **kubectl delete pvc -l app=mysql**

- **Explanation:** By default, Kubernetes does **not** delete PVCs when
  you delete a StatefulSet. This is a safety feature to prevent data
  loss. You must delete them manually if you are finished.

**Command:** **rm nonprod-mysql-ss.yml**

**💡 Best Practices**

1.  **Storage Classes:** In cloud environments (AWS/GCP), ensure you
    have a default StorageClass defined so the volumeClaimTemplates can
    automatically create disks.

2.  **Headless Service:** Always pair a StatefulSet with a \"Headless
    Service\" (ClusterIP: None) to allow Pods to reach each other by
    name for replication.

3.  **Graceful Termination:** Databases need time to flush data to disk.
    Use a high terminationGracePeriodSeconds (e.g., 60s) in the pod spec
    for production.

**Practical Guide: Self-Healing Apps with Readiness & Liveness Probes**

Kubernetes uses probes to monitor the health of your containers. If a
container is struggling, Kubernetes can automatically restart it
(Self-healing) or stop sending it traffic until it recovers.

**🛠 Basic Concepts**

- **Liveness Probe:** Checks if the container is still running. If it
  fails, Kubernetes kills the container and starts a new one (Restarts).

- **Readiness Probe:** Checks if the container is ready to handle
  traffic. If it fails, the Pod is removed from Service endpoints (No
  traffic is sent).

- **Self-Healing:** The ability of the system to detect a failure and
  automatically recover without human intervention.

**🏗 Scenario: Non-Prod Web Application**

You are deploying a web application in the nonprod environment. To
ensure high availability, you need to configure probes that detect when
the app is \"frozen\" or \"not yet ready\" to serve requests.

**Step 1: Define the Application YAML**

We will use a Pod that checks for the existence of specific files to
simulate health.

1.  Create and open a new file: **nano nonprod-probes-app.yml**

2.  Paste the following configuration:

apiVersion: v1

kind: Pod

metadata:

name: nonprod-health-app

labels:

app: health-test

env: nonprod

spec:

containers:

\- name: server-container

image: busybox

\# We create the \'alive\' file on startup, but NOT the \'ready\' file

args:

\- /bin/sh

\- -c

\- touch /tmp/alive; sleep 3600

livenessProbe:

exec:

command:

\- cat

\- /tmp/alive

initialDelaySeconds: 5

periodSeconds: 5

readinessProbe:

exec:

command:

\- cat

\- /tmp/ready

initialDelaySeconds: 5

periodSeconds: 5

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Step 2: Deploy and Observe \"Not Ready\" State**

**Command:** **kubectl apply -f nonprod-probes-app.yml**

**Command:** **kubectl get pods**

- **Explanation:** You will see the Pod is Running but the READY column
  shows 0/1. This is because the **Readiness Probe** is looking for
  /tmp/ready, which does not exist yet. Traffic would not be sent to
  this Pod.

**Step 3: Fix the Readiness State**

Let\'s \"manual trigger\" the app to be ready by creating the file it\'s
looking for.

**Command:** **kubectl exec nonprod-health-app \-- touch /tmp/ready**

**Command:** **kubectl get pods**

- **Explanation:** After a few seconds, the READY column will change to
  1/1. The Pod is now considered healthy and ready to serve traffic.

**Step 4: Test Liveness (Trigger Self-Healing)**

Now we will simulate a \"frozen\" application by deleting the file the
**Liveness Probe** depends on.

**Command:** **kubectl exec nonprod-health-app \-- rm /tmp/alive**

**Command (Watch carefully):** **kubectl get pods -w**

- **Explanation:** 1. The Liveness probe fails because it can\'t cat
  /tmp/alive. 2. Kubernetes identifies the Pod as unhealthy. 3.
  Kubernetes **restarts** the container. 4. Notice the RESTARTS count
  increases to 1. 5. The app is \"healed\" because the startup command
  recreated the /tmp/alive file.

**🔍 Inspection & Troubleshooting**

**Command:** **kubectl describe pod nonprod-health-app**

- **Explanation:** Look at the **Events** section. You will see warnings
  like Liveness probe failed or Readiness probe failed. This is the best
  way to debug why an app is restarting or not receiving traffic.

**🧹 Clean Up Steps**

**Command:** **kubectl delete pod nonprod-health-app**

**Command:** **rm nonprod-probes-app.yml**

**💡 Best Practices**

1.  **InitialDelaySeconds:** Always set this high enough to allow your
    app to finish its internal boot-up (e.g., loading a large database
    into memory).

2.  **Liveness vs. Readiness:** \* Use **Liveness** for total crashes or
    deadlocks.

    - Use **Readiness** for temporary heavy loads or startup delays.

3.  **Don\'t over-probe:** Frequent probes can put a load on your
    application. A periodSeconds of 10-30 is usually sufficient for
    production.

4.  **Avoid External Dependencies:** Don\'t make a probe depend on an
    external database; if the database goes down, all your pods will
    restart simultaneously (a \"cascading failure\").

**Practical Guide: Logging and Monitoring (CLI)**

Observability is a core pillar of DevOps. In Kubernetes, you must know
how to inspect logs to find errors and monitor resource usage
(CPU/Memory) to ensure your applications are performing well.

**🛠 Basic Concepts**

- **Logs (STDOUT/STDERR):** Kubernetes captures everything your
  application prints to the console.

- **Streaming:** Watching logs in real-time as they are generated.

- **Resource Monitoring:** Checking how much CPU and Memory a Pod or
  Node is consuming.

- **Metrics Server:** The cluster component that provides resource usage
  data.

**🏗 Scenario: Non-Prod Debugging**

You are managing a multi-container application in the nonprod
environment. One container generates simulated logs, and you need to
monitor its behavior and resource consumption.

**Step 1: Create a Log-Generating Pod**

We will create a Pod with two containers: a \"web-app\" and a
\"sidecar-logger\".

1.  Create and open a new file: **nano nonprod-log-monitor.yml**

2.  Paste the following configuration:

> apiVersion: v1
>
> kind: Pod
>
> metadata:
>
> name: nonprod-monitor-app
>
> labels:
>
> app: monitor-test
>
> env: nonprod
>
> spec:
>
> containers:
>
> \- name: web-app
>
> image: busybox
>
> args: \[/bin/sh, -c, \'while true; do echo \"SUCCESS: Web request
> handled at \$(date)\"; sleep 2; done\'\]
>
> \- name: sidecar-logger
>
> image: busybox
>
> args: \[/bin/sh, -c, \'while true; do echo \"ERROR: Database
> connection timeout at \$(date)\" \>&2; sleep 5; done\'\]

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Step 2: Deploy and Basic Log Viewing**

**Command:** **kubectl apply -f nonprod-log-monitor.yml**

**Command:** **kubectl logs nonprod-monitor-app -c web-app**

- **Explanation:** \* **logs**: Fetches the logs.

  - **-c web-app**: In multi-container pods, you **must** specify which
    container\'s logs you want to see.

**Step 3: Real-Time Streaming (Follow)**

To watch logs live as they happen (useful for debugging during a test).

**Command:** **kubectl logs -f nonprod-monitor-app -c sidecar-logger**

- **Explanation:** \* **-f**: Stands for \"follow\". It keeps the
  connection open and prints new logs as they arrive.

  - **To Stop:** Press **Ctrl + C**.

**Step 4: Advanced Log Filtering**

Sometimes logs are too long. You can limit the output.

**Command:** **kubectl logs nonprod-monitor-app -c web-app \--tail=5**

- **Explanation:** Only shows the last 5 lines.

**Command:** **kubectl logs nonprod-monitor-app -c web-app \--since=1m**

- **Explanation:** Only shows logs generated within the last 1 minute.

**Step 5: Check Logs of a Crashed Container**

If a pod restarts, the standard logs command shows the *new* container.
To see why the *previous* one died:

**Command:** **kubectl logs nonprod-monitor-app -c web-app -p**

- **Explanation:** \* **-p**: Stands for \"previous\". This retrieves
  logs from the last instance of the container before it crashed or
  restarted.

**Step 6: Resource Monitoring (Top)**

Check the CPU and Memory consumption.

**Command:** **kubectl top pod nonprod-monitor-app**

**Command:** **kubectl top pod nonprod-monitor-app \--containers**

- **Explanation:** \* **top**: Displays CPU (in millicores) and Memory
  (in MiB).

  - **Note:** This command only works if your cluster has the **Metrics
    Server** installed.

**🔍 Inspection Summary**

  -----------------------------------------------------------------------
  **Command**         **Use Case**
  ------------------- ---------------------------------------------------
  kubectl logs        Quick check of recent events.
  \<pod\>             

  kubectl logs -f     Real-time troubleshooting.

  kubectl logs -p     Debugging \"CrashLoopBackOff\" issues.

  kubectl top pod     Investigating performance bottlenecks or OOMKills.
  -----------------------------------------------------------------------

**🧹 Clean Up Steps**

**Command:** **kubectl delete pod nonprod-monitor-app**

**Command:** **rm nonprod-log-monitor.yml**

**💡 Best Practices**

1.  **JSON Logging:** In production, try to output logs in JSON format.
    It makes it easier for tools like ELK or Splunk to index them.

2.  **Log Rotation:** Kubernetes does not store logs forever. If you
    need historical data (older than a few hours/days), you must ship
    them to an external logging system.

3.  **Don\'t Log Secrets:** Ensure your application never prints
    passwords, API keys, or PII (Personally Identifiable Information) to
    the logs.

4.  **Resources:** Always set resources: limits and requests so that
    kubectl top provides meaningful data for capacity planning.

**Practical Guide: Kubernetes Debugging & Troubleshooting Lab**

In this lab, you will act as a DevOps Engineer tasked with fixing a
broken \"Non-Prod\" application. You will learn the \"Investigative
Workflow\" to solve the most common Kubernetes errors.

**🛠 The Troubleshooting Workflow**

1.  **Observe**: kubectl get pods (Check status).

2.  **Describe**: kubectl describe pod \<name\> (Check events and
    configuration).

3.  **Inspect**: kubectl logs \<pod\> (Check application errors).

4.  **Interact**: kubectl exec -it \<pod\> \-- /bin/sh (Verify internal
    state).

**🏗 Scenario: The \"Broken\" Web App**

A developer has deployed a new version of the nonprod-web application,
but it isn\'t working. Your job is to find and fix the three intentional
errors hidden in the configuration.

**Step 1: Create the \"Broken\" Resources**

1.  Create a file named **nonprod-broken-web.yml**

**nano nonprod-broken-web.yml**

2.  Paste the following \"faulty\" configuration:

> apiVersion: apps/v1
>
> kind: Deployment
>
> metadata:
>
> name: nonprod-web
>
> spec:
>
> replicas: 1
>
> selector:
>
> matchLabels:
>
> app: web
>
> template:
>
> metadata:
>
> labels:
>
> app: web
>
> spec:
>
> containers:
>
> \- name: web-container
>
> image: nginx:latst
>
> command: \[\"/bin/sh\", \"-c\", \"echo \'FATAL: Database connection
> failed!\'; exit 1\"\]
>
> readinessProbe:
>
> httpGet:
>
> path: /healthz
>
> port: 80
>
> initialDelaySeconds: 2
>
> periodSeconds: 5

3.  **Save and Exit:** (**Ctrl+O**, **Enter**, **Ctrl+X**).

4.  **Deploy:** **kubectl apply -f nonprod-broken-web.yml**

**Step 2: Debugging \"ImagePullBackOff\"**

**Command:** **kubectl get pods**

- **Symptom:** Pod status is ImagePullBackOff or ErrImagePull.

- **Investigation:** **kubectl describe pod nonprod-web-\<suffix\>**

  - Look at the **Events** at the bottom. You will see: Failed to pull
    image \"nginx:latst\": \... manifest for nginx:latst not found.

- **The Fix:** We must fix the image tag in our configuration file and
  redeploy.

  1.  Edit the YAML file: **nano nonprod-broken-web.yml**

      - Find image: nginx:latst and change it to image: nginx:latest.

  2.  Apply the changes: **kubectl apply -f nonprod-broken-web.yml**

  - **Note:** The Deployment controller will automatically terminate the
    old pod and start a new one with the correct image.

**Step 3: Debugging \"CrashLoopBackOff\"**

**Command:** **kubectl get pods**

- **Symptom:** Status changes to CrashLoopBackOff. The image is now
  correct, but the container keeps dying.

- **Investigation:** **kubectl logs nonprod-web-\<suffix\>**

  - Since the container is constantly restarting, you need to see why
    the *previous* one failed: **kubectl logs nonprod-web-\<suffix\>
    -p**

  - **What you should see:** FATAL: Database connection failed!. This
    confirms the app is crashing because of a bad startup command.

- **The Fix:**

  1.  Edit the YAML file: **nano nonprod-broken-web.yml**

      - Remove the command lines entirely to let Nginx use its default
        startup.

  2.  Apply the changes: **kubectl apply -f nonprod-broken-web.yml**

**Step 4: Debugging \"0/1 READY\" (Probe Failures)**

**Command:** **kubectl get pods**

- **Symptom:** Status is Running, but READY is 0/1.

- **Investigation:** **kubectl describe pod nonprod-web-\<suffix\>**

  - Look at **Events**. You will see: Readiness probe failed: HTTP probe
    failed with statuscode: 404.

- **The Fix:** Nginx doesn\'t have a /healthz page by default. We must
  update the probe path in our YAML.

  1.  Edit the YAML file: **nano nonprod-broken-web.yml**

      - Change the readinessProbe path from /healthz to /.

  2.  Apply the changes: **kubectl apply -f nonprod-broken-web.yml**

  - **Pro-tip:** If the old pod hangs, you can manually force a refresh
    with: **kubectl delete pod \<pod-name\>**.

**Step 5: Final Verification**

**Command:** **kubectl get pods**

- **Success:** Status should be Running and READY should be 1/1.

**🔍 Common Status Cheat Sheet**

  -------------------------------------------------------------------------------
  **Status**             **Likely Cause**                              **Tool to
                                                                       Use**
  ---------------------- --------------------------------------------- ----------
  **ImagePullBackOff**   Typo in image name or private registry auth   describe
                         issue.                                        

  **CrashLoopBackOff**   App is crashing (code error, missing env var, logs -p
                         bad CMD).                                     

  **Pending**            No nodes available, disk (PVC) issues, or     describe
                         CPU/RAM full.                                 

  **Evicted**            Node ran out of disk or memory and kicked the describe
                         pod off.                                      

  **Running (0/1)**      Readiness probe is failing. Traffic is        describe
                         blocked.                                      
  -------------------------------------------------------------------------------

**🧹 Clean Up**

**Command:** **kubectl delete -f nonprod-broken-web.yml** **rm
nonprod-broken-web.yml**

**💡 Best Practices**

1.  **Check Events First:** kubectl get events
    \--sort-by=\'.lastTimestamp\' shows a timeline of cluster problems.

2.  **Resource Limits:** If a pod is being killed randomly, check
    kubectl describe for Last State: Terminated with Reason: OOMKilled
    (Out Of Memory).

3.  **Port-Forward for Local Test:** Use kubectl port-forward
    pod/\<name\> 8080:80 to see if you can reach the app directly,
    bypassing Services.

**Practical Guide: Horizontal Pod Autoscaler (HPA)**

The Horizontal Pod Autoscaler (HPA) automatically updates a workload
resource (like a Deployment), with the aim of automatically scaling the
workload to match demand.

**🛠 Basic Concepts**

- **Horizontal Scaling:** Adding or removing Pods to handle load
  (scaling \"out\" or \"in\").

- **Metrics Server:** The cluster component that provides the CPU/Memory
  data HPA needs to make decisions.

- **Resource Requests:** HPA calculates percentages based on the
  requests defined in your YAML. Without requests, HPA cannot function.

**🏗 Scenario: Non-Prod Web Scale Test**

You are managing a high-traffic web service in the nonprod environment.
During sales or marketing events, traffic spikes. You need to configure
HPA to ensure the application scales up to 10 pods when CPU usage
exceeds 50% and scales back down when the rush is over.

**Step 1: Enable Metrics Server**

HPA requires metrics to function. If you are using Minikube, enable the
addon first.

**Command:** **minikube addons enable metrics-server**

**Step 2: Create the Scalable Deployment**

We will use a php-apache image that performs intensive calculations to
simulate CPU load.

1.  Create and open a new file: **nano nonprod-hpa-app.yml**

2.  Paste the following configuration:

> apiVersion: apps/v1
>
> kind: Deployment
>
> metadata:
>
> name: nonprod-php-apache
>
> spec:
>
> selector:
>
> matchLabels:
>
> run: php-apache
>
> replicas: 1
>
> template:
>
> metadata:
>
> labels:
>
> run: php-apache
>
> spec:
>
> containers:
>
> \- name: php-apache
>
> image: registry.k8s.io/hpa-example
>
> ports:
>
> \- containerPort: 80
>
> resources:
>
> limits:
>
> cpu: 500m
>
> requests:
>
> cpu: 200m
>
> \-\--
>
> apiVersion: v1
>
> kind: Service
>
> metadata:
>
> name: php-apache
>
> labels:
>
> run: php-apache
>
> spec:
>
> ports:
>
> \- port: 80
>
> selector:
>
> run: php-apache

3.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Command to Deploy:** **kubectl apply -f nonprod-hpa-app.yml**

**Step 3: Create the HPA**

**Now we will define the scaling rules using a YAML file. This is the
professional way to manage HPA as it allows for version control.**

1.  Create and open a new file: **nano nonprod-hpa-config.yml**

> Paste the following HPA definition:
>
> apiVersion: autoscaling/v2
>
> kind: HorizontalPodAutoscaler
>
> metadata:
>
> name: nonprod-php-apache-hpa
>
> spec:
>
> scaleTargetRef:
>
> apiVersion: apps/v1
>
> kind: Deployment
>
> name: nonprod-php-apache
>
> minReplicas: 1
>
> maxReplicas: 10
>
> metrics:
>
> \- type: Resource
>
> resource:
>
> name: cpu
>
> target:
>
> type: Utilization
>
> averageUtilization: 50

2.  **To Save and Exit:** Press **Ctrl + O**, then **Enter**, then
    **Ctrl + X**.

**Command to Deploy:** **kubectl apply -f nonprod-hpa-config.yml**

**Step 4: Monitor the HPA**

Open a new terminal or run this command to watch the status.

**Command:** **kubectl get hpa -w**

- **Explanation:**

  - Initially, you might see \<unknown\>/50%. Wait about 1 minute for
    the metrics server to collect data.

  - Once it shows 0%/50%, the system is ready for the load test.

**Step 5: Simulate Load (The \"Stress Test\")**

We will run a temporary \"load-generator\" pod that sends a continuous
stream of requests to the service.

**Command (Run in a NEW terminal):** **kubectl run -it load-generator
\--rm \--image=busybox:1.28 \--restart=Never \-- //bin/sh -c \"while
sleep 0.01; do wget -q -O- http://php-apache; done\"**

**Step 6: Observe Scaling Results**

Go back to the terminal where you are running kubectl get hpa -w.

1.  **Wait 2-3 minutes:** You will see the TARGETS percentage rise
    (e.g., 120%/50%).

2.  **Watch the Replicas:** You will see the REPLICAS count increase
    from 1 to 3, then 6, then potentially 10.

3.  **Verify with Pod list:** **kubectl get pods -l run=php-apache**

**Step 7: Stop the Load and Scale Down**

1.  Go to the load-generator terminal and press **Ctrl + C**.

2.  **Observe:** The CPU usage will drop to 0%.

3.  **Wait:** Kubernetes wait for a \"cool-down\" period (usually 5
    minutes) before scaling down to avoid \"flapping\" (rapidly scaling
    up and down). Eventually, replicas will return to 1.

**🔍 Inspection & Troubleshooting**

**Command:** **kubectl describe hpa nonprod-php-apache-hpa**

- **Explanation:** This is the best way to see why HPA is or isn\'t
  scaling. Check the **Events** section for messages like New size: 4;
  reason: cpu resource utilization (percentage of request) above target.

**🧹 Clean Up Steps**

**Command:** **kubectl delete -f nonprod-hpa-config.yml** **kubectl
delete -f nonprod-hpa-app.yml** **rm nonprod-hpa-app.yml
nonprod-hpa-config.yml**

**💡 Best Practices**

1.  **Define Requests:** HPA cannot calculate percentage usage if
    resources.requests.cpu is missing from the Pod spec.

2.  **Cool-down Periods:** Be patient. Kubernetes scales up fast but
    scales down slowly to ensure the load spike is truly over.

3.  **Declarative Management:** Use YAML (Step 3) instead of kubectl
    autoscale so your scaling logic is documented and repeatable.

**Practical Guide: Kubernetes Storage (PV and PVC)**

In Kubernetes, storage is decoupled from the Pod lifecycle. To save data
permanently, we use a two-layer system for manual management: the
**PersistentVolume** (the \"Physical Disk\") and the
**PersistentVolumeClaim** (the \"Order\").

**🛠 Basic Concepts**

- **PersistentVolume (PV):** A piece of storage in the cluster that has
  been provisioned by an administrator. It represents the actual
  physical storage (like a disk or a folder).

- **PersistentVolumeClaim (PVC):** A request for storage by a user. It
  is similar to a Pod; Pods consume node resources (CPU/RAM), and PVCs
  consume PV resources (Disk).

- **Node Affinity:** A rule that tells Kubernetes exactly which node a
  PV belongs to. This is crucial for multi-node clusters using local
  storage.

**🏗 Scenario: Permanent Data for Non-Prod Apps (Node: minikube-m02)**

You are deploying a legacy application in the nonprod environment. Since
you have a multi-node cluster, you want to ensure the data is stored
specifically on the worker node **minikube-m02**.

**Step 1: Create a PersistentVolume (PV) with Node Affinity**

We will create a 1Gi \"HostPath\" volume. We must add nodeAffinity so
Kubernetes knows this disk only exists on minikube-m02. We also add
type: DirectoryOrCreate to ensure the folder is created automatically.

1.  Create a file named **nonprod-pv.yml**: **nano nonprod-pv.yml**

2.  Paste the following:

> apiVersion: v1
>
> kind: PersistentVolume
>
> metadata:
>
> name: nonprod-manual-pv
>
> spec:
>
> capacity:
>
> storage: 1Gi
>
> volumeMode: Filesystem
>
> accessModes:
>
> \- ReadWriteOnce
>
> persistentVolumeReclaimPolicy: Retain
>
> \# Node Affinity is required for multi-node clusters using hostPath
>
> nodeAffinity:
>
> required:
>
> nodeSelectorTerms:
>
> \- matchExpressions:
>
> \- key: kubernetes.io/hostname
>
> operator: In
>
> values:
>
> \- minikube-m02
>
> hostPath:
>
> path: \"/mnt/data\"
>
> type: DirectoryOrCreate

3.  **Apply:** **kubectl apply -f nonprod-pv.yml**

**Step 2: Create a PersistentVolumeClaim (PVC)**

Now, we act as the application user \"ordering\" the storage we just
defined.

1.  Create a file named **nonprod-pvc.yml**: **nano nonprod-pvc.yml**

2.  Paste the following:

> apiVersion: v1
>
> kind: PersistentVolumeClaim
>
> metadata:
>
> name: nonprod-web-pvc
>
> spec:
>
> accessModes:
>
> \- ReadWriteOnce
>
> resources:
>
> requests:
>
> storage: 1Gi

3.  **Apply:** **kubectl apply -f nonprod-pvc.yml**

**Verify the Bind:** **kubectl get pvc nonprod-web-pvc**

- **Result:** The status should change to Bound.

**Step 3: Deploy a Pod using the Storage**

1.  Create a file named **nonprod-storage-pod.yml**: **nano
    nonprod-storage-pod.yml**

2.  Paste the following:

> apiVersion: v1
>
> kind: Pod
>
> metadata:
>
> name: nonprod-data-pod
>
> spec:
>
> containers:
>
> \- name: web-container
>
> image: nginx
>
> volumeMounts:
>
> \- name: storage-volume
>
> mountPath: /usr/share/nginx/html
>
> volumes:
>
> \- name: storage-volume
>
> persistentVolumeClaim:
>
> claimName: nonprod-web-pvc

3.  **Apply:** **kubectl apply -f nonprod-storage-pod.yml**

**Verify Scheduling:** **kubectl get pod nonprod-data-pod -o wide**

- **Observation:** You will see the Pod is running on minikube-m02.
  Kubernetes automatically placed it there because the PV it requires is
  locked to that node.

**Step 4: The \"Persistence Test\"**

1.  **Write data to the disk:** **kubectl exec nonprod-data-pod \-- sh
    -c \"echo \'Hello from Persistent Storage on m02\' \>
    /usr/share/nginx/html/index.html\"**

2.  **Delete the Pod:** **kubectl delete pod nonprod-data-pod**

3.  **Recreate the Pod:** **kubectl apply -f nonprod-storage-pod.yml**

4.  **Verify the data survived inside the Pod:** **kubectl exec
    nonprod-data-pod \-- cat //usr/share/nginx/html/index.html**

**Step 5: Verify Data on the Physical Node (SSH)**

To truly understand hostPath, we can log into the node itself and see
the file outside of Kubernetes.

1.  **SSH into the worker node:** **minikube ssh -n minikube-m02**

2.  **Navigate to the mapped directory (Create it first if you didn\'t
    use DirectoryOrCreate):** **sudo ls /mnt/data**

3.  **Check the file content on the host disk:** **sudo cat
    /mnt/data/index.html**

4.  **Exit the node session:** **exit**

- **Explanation:** This proves that the data isn\'t \"inside\" the
  container; it is living on the actual hard drive of minikube-m02.

**🔍 Inspection Commands**

  -----------------------------------------------------------------------
  **Command**            **Purpose**
  ---------------------- ------------------------------------------------
  kubectl get pv         List physical volumes and their status.

  kubectl get pvc        List user requests and which PV they are tied
                         to.

  kubectl get pod -o     Check which node your pod is actually running
  wide                   on.
  -----------------------------------------------------------------------

**🧹 Clean Up**

**Command:** **kubectl delete pod nonprod-data-pod** **kubectl delete
pvc nonprod-web-pvc** **kubectl delete pv nonprod-manual-pv**

**💡 Best Practices**

1.  **Node Affinity for Local Storage:** Without nodeAffinity, a Pod
    might try to start on the master node and fail to find the /mnt/data
    folder located on the worker node.

2.  **DirectoryOrCreate:** Always use type: DirectoryOrCreate in your PV
    spec if you want Kubernetes to create the host folder automatically.

3.  **Reclaim Policy:** Use Retain so that if you accidentally delete
    your PVC, the PV (and the data on disk) is kept safe until an admin
    manually cleans it up.

**Practical Guide: Managing Application Configuration with ConfigMaps**

ConfigMaps allow you to decouple environment-specific configuration from
your container images. This makes your applications portable across
different environments (Dev, QA, Prod) without changing the code.

**🛠 Basic Concepts**

- **ConfigMap:** A Kubernetes object used to store non-confidential data
  in key-value pairs.

- **Environment Variables:** Injecting specific keys from a ConfigMap as
  variables inside a container.

- **Volume Mounts:** Mounting an entire ConfigMap as a set of files
  inside a specific directory in the container.

**🏗 Scenario: Configuring a Non-Prod Web Server**

You are deploying a web application that needs to know its \"Environment
Name\" and a \"Welcome Message.\" You will store these settings in a
ConfigMap and use them in a Pod.

**Step 1: Create a ConfigMap (Three Methods)**

**Method A: Using CLI Literals (Fastest)**

**Command:** **kubectl create configmap nonprod-config
\--from-literal=APP_COLOR=blue \--from-literal=APP_MODE=debug**

**Method B: From a Property File**

1.  Create a config file: **nano app-settings.properties**

2.  Paste:

> welcome_message=Welcome to the Non-Prod Portal
>
> log_level=info

3.  **Create:** **kubectl create configmap nonprod-file-config
    \--from-file=app-settings.properties**

**Method C: Using YAML (Best for Production)**

1.  Create **nonprod-config.yml**:

> apiVersion: v1
>
> kind: ConfigMap
>
> metadata:
>
> name: web-config
>
> namespace: default
>
> data:
>
> UI_THEME: \"dark\"
>
> MAX_CONNECTIONS: \"100\"

2.  **Apply:** **kubectl apply -f nonprod-config.yml**

**Step 2: Use ConfigMap as Environment Variables**

We will create a Pod that pulls values from the web-config we just
created.

1.  Create **config-env-pod.yml**:

> apiVersion: v1
>
> kind: Pod
>
> metadata:
>
> name: config-env-pod
>
> spec:
>
> containers:
>
> \- name: test-container
>
> image: busybox
>
> command: \[ \"/bin/sh\", \"-c\", \"env\" \]
>
> envFrom:
>
> \- configMapRef:
>
> name: web-config

2.  **Apply:** **kubectl apply -f config-env-pod.yml**

3.  **Verify:** **kubectl logs config-env-pod \| grep THEME**

**Step 3: Updating a ConfigMap**

When you update a ConfigMap, values used as **Volume Mounts** will
eventually update inside the container (usually within a minute). Values
used as **Environment Variables** will NOT update until the Pod is
restarted.

1.  Edit the ConfigMap: **kubectl edit configmap web-config**

2.  Change UI_THEME to light.

3.  Wait 30-60 seconds and check the volume-mounted pod: **kubectl exec
    config-vol-pod \-- cat /etc/config/UI_THEME**

**🔍 Inspection & Troubleshooting**

  -----------------------------------------------------------------------
  **Command**                  **Purpose**
  ---------------------------- ------------------------------------------
  kubectl get cm               List all ConfigMaps.

  kubectl describe cm          View the data keys and values inside the
  web-config                   ConfigMap.

  kubectl get cm web-config -o View the full YAML definition of the
  yaml                         ConfigMap.
  -----------------------------------------------------------------------

**🧹 Clean Up**

**Command:** **kubectl delete pod config-env-pod config-vol-pod**
**kubectl delete cm web-config nonprod-config nonprod-file-config**

**💡 Best Practices**

1.  **Immutability:** In newer K8s versions, you can set immutable: true
    in your ConfigMap. This prevents accidental changes and improves
    performance.

2.  **Size Limit:** ConfigMaps are stored in etcd and have a 1MiB size
    limit. Don\'t use them for large files.

3.  **Not for Secrets:** Never store passwords, tokens, or SSH keys in a
    ConfigMap. Use **Secrets** instead (they are base64 encoded and can
    be encrypted at rest).

4.  **Prefixing:** Always prefix your ConfigMap names with the
    environment (e.g., dev-db-config) to avoid collisions.

**Practical Guide: Managing Sensitive Data with Kubernetes Secrets**

Secrets are used to store and manage sensitive information, such as
passwords, OAuth tokens, and ssh keys. Storing confidential information
in a Secret is safer and more flexible than putting it verbatim in a Pod
definition or a container image.

**🛠 Basic Concepts**

- **Secret:** An object that contains a small amount of sensitive data
  such as a password, a token, or a key.

- **Base64 Encoding:** Secrets are stored as base64-encoded strings
  (this is NOT encryption, just a way to handle binary data).

- **Opaque Secrets:** The default type for user-defined arbitrary
  key-value pairs.

**🏗 Scenario: Securing Database Credentials**

You are deploying a database-driven application in the nonprod
environment. You need to store the database root password and an API key
securely so that only the application can access them.

**Step 1: Create a Secret (Three Methods)**

**Method A: Using CLI Literals (Fastest)**

**Command:** **kubectl create secret generic nonprod-db-secret
\--from-literal=DB_PASSWORD=SuperSecret123**

**Method B: From a File**

This is the safest way to avoid leaving passwords in your terminal
history.

1.  Create a password file: **echo -n \'my-api-key-xyz-789\' \>
    api-key.txt**

2.  **Create:** **kubectl create secret generic nonprod-api-secret
    \--from-file=api-key.txt**

**Method C: Using YAML (Best for GitOps)**

Secrets in YAML must be **Base64 encoded** beforehand.

1.  **Encode your value:** **echo -n \'admin-pass\' \| base64** (Copy
    the output, e.g., YWRtaW4tcGFzcw==)

2.  Create **nonprod-secret.yml**:

> apiVersion: v1
>
> kind: Secret
>
> metadata:
>
> name: web-secret
>
> type: Opaque
>
> data:
>
> DB_USER: YWRtaW4= \# \'admin\' in base64
>
> DB_PASS: YWRtaW4tcGFzcw== \# \'admin-pass\' in base64

3.  **Apply:** **kubectl apply -f nonprod-secret.yml**

**Step 2: Use Secret as Environment Variables**

1.  Create **secret-env-pod.yml**:

> apiVersion: v1
>
> kind: Pod
>
> metadata:
>
> name: secret-env-pod
>
> spec:
>
> containers:
>
> \- name: app-container
>
> image: busybox
>
> command: \[\"/bin/sh\", \"-c\", \"echo \'The password is loaded in
> ENV\'; env; sleep 3600\"\]
>
> env:
>
> \- name: DATABASE_PASSWORD
>
> valueFrom:
>
> secretKeyRef:
>
> name: web-secret
>
> key: DB_PASS

2.  **Apply:** **kubectl apply -f secret-env-pod.yml**

3.  **Verify: ** kubectl exec -it secret-env-pod \-- env

**Step 3: Decoding a Secret (Admin Troubleshooting)**

If you need to verify what is actually inside a secret:

**Command:** **kubectl get secret web-secret -o
jsonpath=\'{.data.DB_PASS}\' \| base64 \--decode**

**🔍 Inspection & Troubleshooting**

  -----------------------------------------------------------------------
  **Command**                   **Purpose**
  ----------------------------- -----------------------------------------
  kubectl get secrets           List all secrets in the namespace.

  kubectl describe secret       See the keys (but not values) and the
  web-secret                    size of the data.

  kubectl get secret web-secret See the base64 encoded data.
  -o yaml                       
  -----------------------------------------------------------------------

**🧹 Clean Up**

**Command:** **kubectl delete pod secret-env-pod secret-vol-pod**
**kubectl delete secret web-secret nonprod-db-secret
nonprod-api-secret** **rm api-key.txt nonprod-secret.yml
secret-env-pod.yml**

**💡 Best Practices**

1.  **Not Encrypted by Default:** Base64 is NOT encryption. To be truly
    secure, you must enable **Encryption at Rest** in your cluster\'s
    ETCD settings.

2.  **RBAC Control:** Limit who can \"get\" or \"describe\" secrets
    using Kubernetes Roles and RoleBindings.

3.  **External Secrets:** In high-security production environments, use
    tools like **HashiCorp Vault** or **AWS Secrets Manager** with the
    \"External Secrets Operator\" to sync data into Kubernetes.

4.  **Avoid Git:** Never commit YAML files containing Base64 secrets to
    public repositories. Use a tool like sealed-secrets if you must
    store them in Git.

**Practical Guide: Managing Kubernetes Access (RBAC)**

Role-Based Access Control (RBAC) is the standard method for regulating
access to computer or network resources based on the roles of individual
users within an enterprise. In Kubernetes, it dictates \"who\" can do
\"what\" and \"where.\"

**🛠 Basic Concepts**

- **User Account:** Represents a human user. Kubernetes does not manage
  these directly (no User objects). It relies on external identity
  providers or certificates.

- **Service Account (SA):** Represents a machine/application identity
  managed directly by Kubernetes.

- **Role:** A set of permissions (e.g., read pods, delete services)
  restricted to a specific **Namespace**.

- **RoleBinding:** Connects a Role to a User or Service Account within
  that Namespace.

- **ClusterRole:** A set of permissions applied across the **entire
  Cluster** (e.g., view nodes, manage namespaces).

- **ClusterRoleBinding:** Connects a ClusterRole to a User or Service
  Account cluster-wide.

**🏗 Scenario 1: Application Permissions (Namespace Level)**

You are deploying an application in the rbac-lab namespace. The
application needs a Service Account that is only allowed to view
(read/list) Pods within that specific namespace.

**Step 1: Create the Namespace and Service Account**

First, we create an isolated environment and the identity our
application will use.

**Commands:** **kubectl create namespace rbac-lab**

**kubectl create serviceaccount app-reader-sa -n rbac-lab**

**Step 2: Create the Role**

Now, we define the exact permissions. We want this role to only allow
get, list, and watch actions on pods.

**Command:** kubectl api-resources -o wide

**kubectl create role pod-reader \--verb=get,list,watch \--resource=pods
-n rbac-lab**

- **Explanation:** This creates a Role named pod-reader in the rbac-lab
  namespace. It cannot modify pods or view secrets; it can only read pod
  data.

**Step 3: Create the RoleBinding**

We must connect our app-reader-sa (the \"who\") to the pod-reader role
(the \"what\").

**Command:** **kubectl create rolebinding bind-pod-reader
\--role=pod-reader \--serviceaccount=rbac-lab:app-reader-sa -n
rbac-lab**

**Step 4: Test the Permissions**

Kubernetes provides a built-in testing tool called auth can-i to verify
permissions without actually logging in as the service account.

1.  **Test if the SA can list pods (Should be YES):** **kubectl auth
    can-i list pods -n rbac-lab
    \--as=system:serviceaccount:rbac-lab:app-reader-sa**

2.  **Test if the SA can delete pods (Should be NO):** **kubectl auth
    can-i delete pods -n rbac-lab
    \--as=system:serviceaccount:rbac-lab:app-reader-sa**

3.  **Test if the SA can list pods in the default namespace (Should be
    NO):** **kubectl auth can-i list pods -n default
    \--as=system:serviceaccount:rbac-lab:app-reader-sa**

**🏗 Scenario 2: Human User Permissions (Cluster Level)**

A new developer, \"Jane\", joined the team. She needs cluster-wide
permission to list all physical Nodes in the cluster to check their
health. Since Nodes are not bound to a namespace, we must use a
**ClusterRole**.

*Note: Since Kubernetes doesn\'t have a User creation command, we will
simulate Jane\'s identity using the \--as=jane flag.*

**Step 1: Create the ClusterRole (YAML)**

We define a role that allows viewing node resources across the entire
cluster using a YAML manifest.

1.  Create a file named **clusterrole.yml**: **nano clusterrole.yml**

2.  Paste the following configuration:

> apiVersion: rbac.authorization.k8s.io/v1
>
> kind: ClusterRole
>
> metadata:
>
> name: node-viewer
>
> rules:
>
> \- apiGroups: \[\"\"\]
>
> resources: \[\"nodes\"\]
>
> verbs: \[\"get\", \"list\", \"watch\"\]

3.  **Apply:** **kubectl apply -f clusterrole.yml**

- **Explanation:** Notice we do NOT specify a namespace, because nodes
  and ClusterRoles are cluster-scoped.

**Step 2: Create the ClusterRoleBinding (YAML)**

We bind the node-viewer ClusterRole to the user \"jane\" using another
YAML manifest.

1.  Create a file named **clusterrolebinding.yml**: **nano
    clusterrolebinding.yml**

2.  Paste the following configuration:

> apiVersion: rbac.authorization.k8s.io/v1
>
> kind: ClusterRoleBinding
>
> metadata:
>
> name: jane-node-access
>
> subjects:
>
> \- kind: User
>
> name: jane
>
> apiGroup: rbac.authorization.k8s.io
>
> roleRef:
>
> kind: ClusterRole
>
> name: node-viewer
>
> apiGroup: rbac.authorization.k8s.io

3.  **Apply:** **kubectl apply -f clusterrolebinding.yml**

**Step 3: Test the Cluster-Wide Permissions**

1.  **Test if Jane can list nodes (Should be YES):** **kubectl auth
    can-i list nodes \--as=jane**

2.  **Test if Jane can list secrets (Should be NO):** **kubectl auth
    can-i list secrets \--as=jane**

**🔍 Inspection & Troubleshooting**

  -----------------------------------------------------------------------
  **Command**                      **Purpose**
  -------------------------------- --------------------------------------
  kubectl get roles -A             List all available Roles across all
                                   namespaces in the system.

  kubectl get clusterroles         List all available ClusterRoles in the
                                   entire system.

  kubectl get serviceaccounts -n   List SAs in the namespace.
  rbac-lab                         

  kubectl describe role pod-reader See the specific API groups,
  -n rbac-lab                      resources, and verbs allowed by the
                                   Role.

  kubectl describe rolebinding     See exactly who is bound to the Role.
  bind-pod-reader -n rbac-lab      

  kubectl auth can-i \--list       List EVERYTHING the user \"jane\" is
  \--as=jane                       allowed to do.
  -----------------------------------------------------------------------

**🧹 Clean Up**

To keep your cluster clean, remove the lab resources.

**Command:** **kubectl delete -f clusterrolebinding.yml** **kubectl
delete -f clusterrole.yml** **kubectl delete namespace rbac-lab** **rm
clusterrole.yml clusterrolebinding.yml**

*(Deleting the namespace automatically deletes the Role, RoleBinding,
and ServiceAccount inside it).*

**💡 Best Practices**

1.  **Principle of Least Privilege:** Never grant cluster-admin or
    wildcard (\*) permissions unless absolutely necessary. Grant only
    the verbs (get, list) and resources (pods) required.

2.  **Use ServiceAccounts for Pods:** Every Pod uses the default service
    account if one isn\'t specified. Always create dedicated SAs for
    your applications to limit their blast radius if compromised.

3.  **Roles over ClusterRoles:** Default to using Roles
    (namespace-scoped) whenever possible to prevent users or apps from
    accidentally breaking resources in other environments.
