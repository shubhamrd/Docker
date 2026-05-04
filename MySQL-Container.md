## MySQL Container Practice

**Objective:**

Create and interact with a MySQL container, demonstrating environment variable configuration, data persistence, and basic database operations.

### Practice Steps

1. **Create MySQL Container**
   ```bash
   docker run \
       --name db1 \
       -d \
       -e MYSQL_ROOT_PASSWORD=password123 \
       -e MYSQL_DATABASE=blankdb \
       -e MYSQL_USER=shubham \
       -e MYSQL_PASSWORD=shubham123 \
       public.ecr.aws/docker/library/mysql:latest
   ```
   *Runs MySQL in detached mode with a root password, a default database, and a dedicated user.*

2. **Verify the Container is Running**
   ```bash
   docker ps -a
   ```

3. **View Container Logs**
   ```bash
   docker logs db1
   ```

4. **Connect to the MySQL Instance (Option 1 – Interactive Shell)**
   ```bash
   docker exec -it db1 bash
   ```
   Inside the container:
   ```bash
   mysql -u root -ppassword123
   ```
   ```sql
   SHOW DATABASES;
   CREATE DATABASE newdb;
   SHOW DATABASES;
   EXIT;
   ```
   Exit the container shell with `exit`.

5. **Connect to the MySQL Instance (Option 2 – Direct Exec)**
   ```bash
   docker exec -it db1 mysql -u root -ppassword123
   ```
   ```sql
   SHOW DATABASES;
   EXIT;
   ```

6. **Connect as the Created User**
   ```bash
   docker exec -it db1 mysql -u shubham -pshubham123
   ```
   ```sql
   SHOW DATABASES;
   EXIT;
   ```

7. **Clean Up**
   ```bash
   docker stop db1
   docker rm db1
   ```
