## Docker Installation

1. **Update System**
   ```bash
   sudo yum -y remove buildah podman
   ```
   This command updates the system to ensure you have the latest packages and dependencies.

2. **Install Required Dependencies**
   ```bash
   sudo yum install -y yum-utils
   ```
   Install necessary packages for Docker.

3. **Add Docker Repository**
   ```bash
   sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
   ```
   This adds the Docker repository to your system.

4. **Install Docker Engine**
   ```bash
   sudo yum install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
   ```
   Install Docker Engine using the added repository.

5. **Start and Enable Docker**
   ```bash
   sudo systemctl start docker
   sudo systemctl enable docker
   sudo systemctl status docker
   ```
   Start and enable Docker to ensure it runs on system boot.

6. **Verify Docker Installation**
   ```bash
   docker --version
   ```
   Check the installed Docker version to confirm a successful installation.

7. **Run Hello World Container**
   ```bash
   docker run hello-world
   ```
   Verify Docker works correctly.

8. **Basic Docker Commands**
   ```bash
   docker ps          # List running containers
   docker ps -a       # List all containers
   docker images      # List images
   ```