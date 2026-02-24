📊 3D Data Visualization on a UGREEN NAS

This project demonstrates how to load a large dataset (~10 million rows), analyze it using Python, and generate an interactive 3D visualization served via an Nginx web server running on a UGREEN NAS using Docker.

🧰 Prerequisites

Hardware

UGREEN NAS with Docker support

Access

SSH access to the NAS

Docker & Docker Compose installed

Dataset
Example dataset used:

gaming_mental_health_10M_40features.csv (~170 MB)
📁 Directory Structure

Create a dedicated workspace on the NAS:

mkdir -p /volume1/docker/csv3d/work
mkdir -p /volume1/docker/csv3d/nginx_html

Copy the dataset into the working directory:

cp /volume1/datasets/gaming_mental_health_10M_40features.csv \
   /volume1/docker/csv3d/work/datensatz.csv
🐳 Docker Compose Setup

Create the file:

/volume1/docker/csv3d/docker-compose.yml
services:

  jupyter:
    image: jupyter/datascience-notebook:latest
    container_name: csv3d-jupyter
    ports:
      - "8890:8888"
    volumes:
      - ./work:/home/jovyan/work
    environment:
      - JUPYTER_TOKEN=csv3d
    restart: unless-stopped

  nginx:
    image: nginx:alpine
    container_name: csv3d-nginx
    ports:
      - "8085:80"
    volumes:
      - ./nginx_html:/usr/share/nginx/html:ro
    restart: unless-stopped

Start the containers:

cd /volume1/docker/csv3d
sudo docker compose up -d
🧪 Data Analysis & 3D Plot

Open Jupyter in your browser:

http://<NAS-IP>:8890
Token: csv3d

Create:

work/make_plot.py
import pandas as pd
import plotly.express as px

df = pd.read_csv("datensatz.csv", sep="\t")

MAX_POINTS = 150_000
if len(df) > MAX_POINTS:
    df = df.sample(MAX_POINTS, random_state=1)

fig = px.scatter_3d(
    df,
    x="age",
    y="daily_gaming_hours",
    z="stress_level",
    color="anxiety_score",
    title="Gaming Mental Health Analysis",
    opacity=0.7
)

fig.write_html("plot3d.html", include_plotlyjs="cdn")
print("plot3d.html created.")

Run:

cd work
python make_plot.py
🌐 Publish Visualization
sudo cp /volume1/docker/csv3d/work/plot3d.html \
        /volume1/docker/csv3d/nginx_html/

Open:

http://<NAS-IP>:8085/plot3d.html
🛠 Troubleshooting
Problem	Cause	Fix
permission denied (Docker)	Missing Docker rights	Run with sudo
bind: address already in use	Port conflict	Change host port
Dataset loads incorrectly	File uses tabs	Use sep="\t"
Browser freezes/crashes	Too many points	Reduce sample size
📈 Optional: Correlation Matrix
import numpy as np
import seaborn as sns
import matplotlib.pyplot as plt

numeric_df = df.select_dtypes(include=[np.number])

plt.figure(figsize=(12, 8))
sns.heatmap(numeric_df.corr(), cmap="coolwarm", center=0)
plt.show()
