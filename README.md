<!-- vscode-markdown-toc -->
* 1. [🍺 Installing Homebrew](#InstallingHomebrew)
* 2. [🐳 Installing Docker Engine with Colima](#InstallingDockerEnginewithColima)
	* 2.1. [Prerequisites](#Prerequisites)
	* 2.2. [Installation Steps](#InstallationSteps)
	* 2.3. [Basic Colima Commands](#BasicColimaCommands)
* 3. [🐳 Installing Portainer](#InstallingPortainer)
		* 3.1. [Create a folder to use as a volume for the Portainer](#CreateafoldertouseasavolumeforthePortainer)
	* 3.1. [Option 1: Using Docker Run](#Option1:UsingDockerRun)
	* 3.2. [Option 2: Using Docker Compose](#Option2:UsingDockerCompose)
	* 3.3. [Accessing Portainer](#AccessingPortainer)
	* 3.4. [Troubleshooting](#Troubleshooting)
* 4. [Cleaning Up](#CleaningUp)

<!-- vscode-markdown-toc-config
	numbering=true
	autoSave=true
	/vscode-markdown-toc-config -->
<!-- /vscode-markdown-toc --># Colima with Docker Engine Setup

##  1. <a name='InstallingHomebrew'></a>🍺 Installing Homebrew

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

# Colima with Docker Engine Setup

##  2. <a name='InstallingDockerEnginewithColima'></a>🐳 Installing Docker Engine with Colima

Colima provides a Docker Engine environment without needing Docker Desktop. Here's how to set it up:

###  2.1. <a name='Prerequisites'></a>Prerequisites

- macOS
- Homebrew (package manager for macOS)

###  2.2. <a name='InstallationSteps'></a>Installation Steps

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

###  2.3. <a name='BasicColimaCommands'></a>Basic Colima Commands

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

##  3. <a name='InstallingPortainer'></a>🐳 Installing Portainer

####  3.1. <a name='CreateafoldertouseasavolumeforthePortainer'></a>Create a folder to use as a volume for the Portainer
```bash
mkdir ./docker/projects/k8s/colima-with-docker-engine/portainer_data
```

###  3.1. <a name='Option1:UsingDockerRun'></a>Option 1: Using Docker Run

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

###  3.2. <a name='Option2:UsingDockerCompose'></a>Option 2: Using Docker Compose

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

###  3.3. <a name='AccessingPortainer'></a>Accessing Portainer

- Open your browser and navigate to:
  - HTTPS: `https://localhost:9443`
  - HTTP: `http://localhost:8000`
- Create your admin user on first login

###  3.4. <a name='Troubleshooting'></a>Troubleshooting

```bash
# Check Colima status
colima status

# View Portainer logs
docker logs portainer

# Check Portainer container status
docker ps | grep portainer
```

---

##  4. <a name='CleaningUp'></a>Cleaning Up

To remove everything:

```bash
# Stop Portainer
docker-compose down -v

# Stop Colima
colima stop

# Delete Colima instance
colima delete
```