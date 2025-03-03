- [Colima with Docker Engine Setup](#colima-with-docker-engine-setup)
  - [🍺 Installing Homebrew](#-installing-homebrew)
  - [🐳 Installing Docker Engine with Colima](#-installing-docker-engine-with-colima)
    - [Installation Steps](#installation-steps)
    - [Basic Colima Commands](#basic-colima-commands)
  - [🐳 Installing Portainer](#-installing-portainer)
      - [Create a folder to use as a volume for the Portainer](#create-a-folder-to-use-as-a-volume-for-the-portainer)
    - [Option 1: Using Docker Run](#option-1-using-docker-run)
    - [Option 2: Using Docker Compose](#option-2-using-docker-compose)
    - [Accessing Portainer](#accessing-portainer)
    - [Troubleshooting](#troubleshooting)
  - [Cleaning Up](#cleaning-up)

# Colima with Docker Engine Setup

## 🍺 Installing Homebrew

Before installing Colima, you'll need Homebrew package manager. Here's how to install it:

1. **Install Homebrew**

   ```bash
   /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
   ```

2. **Add Homebrew to PATH**

   For MacOS with Apple Silicon (M1/M2):
   ```bash
   echo 'eval "$(/opt/homebrew/bin/brew shellenv)"' >> ~/.zshrc
   source ~/.zshrc
   ```

   For Intel Mac:
   ```bash
   echo 'eval "$(/usr/local/bin/brew shellenv)"' >> ~/.zshrc
   source ~/.zshrc
   ```

3. **Verify Installation**

   ```bash
   brew --version
   ```

---

## 🐳 Installing Docker Engine with Colima

Colima provides a Docker Engine environment without needing Docker Desktop. Here's how to set it up:

### Installation Steps

1. **Install Colima**

   ```bash
   brew install colima
   ```

2. **Start Colima**

   ```bash
   colima start
   ```

3. **Verify Installation**

   ```bash
   docker ps
   docker info
   ```

### Basic Colima Commands

```bash
# Start Colima
colima start

# Stop Colima
colima stop

# Delete Colima instance
colima delete

# Check status
colima status
```

---

## 🐳 Installing Portainer

#### Create a folder to use as a volume for the Portainer
```bash
mkdir ./docker/projects/k8s/colima-with-docker-engine/portainer_data
```

### Option 1: Using Docker Run

```bash
docker volume create portainer_data
docker run -d \
  -p 8000:8000 \
  -p 9443:9443 \
  --name portainer \
  --restart=always \
  -v /var/run/docker.sock:/var/run/docker.sock \
  -v portainer_data:/data \
  portainer/portainer-ce:latest
```

### Option 2: Using Docker Compose

Create a docker-compose.yml file:

```yaml
// filepath: ./docker/projects/k8s/colima-with-docker-engine/docker-compose.yml

services:
  portainer:
    image: portainer/portainer-ce:latest
    container_name: portainer
    restart: always
    security_opt:
      - no-new-privileges:true
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - portainer_data:/data
    ports:
      - 8000:8000
      - 9443:9443

volumes:
  portainer_data:
```

Start Portainer using Docker Compose:

```bash
docker-compose up -d
```

### Accessing Portainer

- Open your browser and navigate to:
  - HTTPS: `https://localhost:9443`
  - HTTP: `http://localhost:8000`
- Create your admin user on first login

### Troubleshooting

```bash
# Check Colima status
colima status

# View Portainer logs
docker logs portainer

# Check Portainer container status
docker ps | grep portainer
```

---

## Cleaning Up

To remove everything:

```bash
# Stop Portainer
docker-compose down -v

# Stop Colima
colima stop

# Delete Colima instance
colima delete
```