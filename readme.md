# Air-Gapped Docker Setup with Squid Proxy

This repository provides a **Docker Compose** setup containing:

1. A **Squid proxy** container (with a configurable whitelist).  
2. An **air-gapped container** that routes all outbound traffic through the Squid proxy.  

The **air-gapped** container can only reach domains explicitly whitelisted in the Squid config. This is useful for scenarios such as AI model downloads (GitHub, PyPI) while preventing general internet access.

---

## Contents

1. [Overview](#overview)  
2. [Prerequisites](#prerequisites)  
3. [Setup and Usage](#setup-and-usage)
4. [Configuration](#Configuration)

---

## Overview

- **Squid Proxy**: Runs on port `3128` (internally), enforcing domain-based ACL rules.  
- **Air-Gapped Container**:  
  - Installs Python, Git, etc.  
  - Uses environment variables (or global Git config) so all web requests (pip, git clone, curl) go through the proxy.  
- **Network Isolation**: The `airgapped_server` container cannot reach the outside unless the proxy explicitly allows it.

---

## Prerequisites

- Docker (Engine >= 20.x)  
- Docker Compose (Plugin >= 2.x or docker-compose >= 1.29)  

A basic understanding of Dockerfiles, Docker Compose, and networking is recommended.

---

## Setup and Usage

### 1. Building the Images

Clone or download this repository, then navigate to the project folder:
```bash
cd path/to/this/project
```
Run:
```bash
docker-compose build
```
This step will:
* Build the Squid image (copying in squid.conf and allowed_sites.txt).
* Build the air-gapped image (installing Python, pip, etc.).

### 2. Running the Containers

Once built, start them in the background:
```bash
    docker-compose up -d
```
You should see logs indicating the two containers (proxy, airgapped_deepseek) have started.

### 3. Shelling into the Air-Gapped Container

To open an interactive Bash shell inside the air-gapped container, run:

```bash
docker-compose exec airgapped_deepseek bash
```
Now you can:
* pip install packages (if PyPI is whitelisted).
* git clone repositories (if GitHub is whitelisted).
* curl or ping to test connectivity.

Example:
```bash
# Inside the container:
py --version
pip install flask
git clone https://github.com/your-org/your-repo.git
```

## Configuration
### Editing the whitelist of allowed sites
1. Locate the white list file by default it should be at `./proxy/allowed_sites.txt`.
```
.github.com
.githubusercontent.com
.pypi.org
.pythonhosted.org
```
2. Add any new domains you want to allow. For example, to allow huggingface.co, add:

```
.huggingface.co
```
3. Rebuild and restart the containers:

```bash
docker-compose up -d --build
```
4. the air gapped server should now have access to the new domain in the whitelist. `ping` and `curl` are included to help validate your proxy configurations work
### Verifying Access
1. Inside the air-gapped container:
```bash
curl https://pypi.org/simple/
# If pypi.org is in the whitelist, you should see HTML for PyPI's simple index.
```
2. Blocked Example:
```bash
curl https://www.google.com
# Expect a 403 Forbidden or "Access Denied" if google.com isn't whitelisted.
```






