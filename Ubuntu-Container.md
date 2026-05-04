## Create a Ubuntu Container

1. **Pull a Base Image**
   ```bash
   docker pull ubuntu
   ```
   Choose a base image from Docker Hub.

2. **Run a Container**
   ```bash
   docker run -itd --name ubuntu-container ubuntu
   ```
   Start a basic container.

3. **Explore the Container**
   ```bash
   ls
   cat /etc/os-release
   ```
   Explore its filesystem.

4. **Exit the Container**
   ```bash
   exit
   ```

5. **View Exited Containers**
   ```bash
   docker ps -a
   ```

6. **Stop and Remove the Container**
   ```bash
   docker stop ubuntu-container
   docker rm ubuntu-container
   ```