## Create a CentOS Container

1. **Pull a Base Image**
   ```bash
   docker pull centos:8
   ```
   Choose a base image from Docker Hub.

2. **Run a Container**
   ```bash
   docker run -it centos:8
   ```
   Start a basic container without specifying a command.

3. **Explore the Container**
   ```bash
   ls
   cat /etc/os-release
   ```
   Explore its filesystem or execute commands.

4. **Exit the Container**
   ```bash
   exit
   ```

5. **View Exited Containers**
   ```bash
   docker ps -a
   ```

6. **Start the Stopped Container**
   ```bash
   docker start <container-id>
   docker ps
   ```

7. **Connect to the Container**
   ```bash
   docker exec -it <container-id> bash
   ```
   ```bash
   exit
   ```

8. **Remove Containers**
   ```bash
   docker stop <container-id>
   docker rm <container-id>
   ```