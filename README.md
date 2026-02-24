# 📊 3D Data Visualization on a UGREEN NAS

This project demonstrates how to load a large dataset (~10 million rows), analyze it using Python, and generate an interactive 3D visualization served via an Nginx web server running on a UGREEN NAS using Docker.

---

## 🧰 Prerequisites

**Hardware**

- UGREEN NAS with Docker support

**Access**

- SSH access to the NAS  
- Docker & Docker Compose installed

**Dataset**

Example dataset used:

gaming_mental_health_10M_40features.csv (~170 MB)

---

## 📁 Directory Structure

Create workspace directories:

```bash
mkdir -p /volume1/docker/csv3d/work
mkdir -p /volume1/docker/csv3d/nginx_html
