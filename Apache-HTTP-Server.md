## Hosting a Website with Apache HTTP Server Container

**Objective:**

Provide hands‑on experience with Docker by hosting a website using the Apache HTTP Server container, modifying website content inside the container, and utilizing port forwarding.

### Practice Steps

1. **Pull Apache HTTP Server Container**
   ```bash
   docker pull public.ecr.aws/docker/library/httpd:latest
   ```

2. **Run Apache HTTP Server Container**
   ```bash
   docker run -d -p 8080:80 \
       --name my-apache-container \
       public.ecr.aws/docker/library/httpd:latest
   ```
   *Runs the container in detached mode, maps host port 8080 to container port 80, and names the container.*

3. **Verify the Running Container**
   ```bash
   docker ps
   ```
   Ensure the Apache container appears in the list.

4. **Access the Website**
   Open a browser and navigate to `http://<host-ip>:8080` (e.g., `http://192.168.137.128:8080`). You should see the default Apache welcome page.

5. **Enter the Container**
   ```bash
   docker exec -it my-apache-container /bin/bash
   ```
   This opens an interactive shell inside the container.

6. **Change `index.html` Content**
   ```bash
   echo "<h1>Welcome to My Dockerized Website</h1>" > /usr/local/apache2/htdocs/index.html
   ```
   Replace the default page with a custom message.

7. **Exit the Container**
   ```bash
   exit
   ```

8. **Refresh the Website**
   Reload `http://<host-ip>:8080` in your browser to see the updated content.

9. **Stop and Remove the Container**
   ```bash
   docker stop my-apache-container
   docker rm my-apache-container
   ```
