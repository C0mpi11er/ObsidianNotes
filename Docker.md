Absolutely! Here's your 🔥 **Docker Cheat Sheet** — concise, structured, and ready for your notes (e.g. in Obsidian):

---

# 🐳 **Docker Cheat Sheet**

> 🚀 Quick reference for image handling, containers, volumes, networking, and real-world examples.

---

## 📦 Images

|Task|Command|
|---|---|
|Search for image|`docker search <name>`|
|Pull image|`docker pull <image>`|
|List images|`docker images`|
|Remove image|`docker rmi <image>`|
|Build image (from Dockerfile)|`docker build -t <name> .`|

---

## 🚀 Containers

|Task|Command|
|---|---|
|Run container (interactive)|`docker run -it <image>`|
|Run in background (detached)|`docker run -d <image>`|
|Run and auto-remove|`docker run --rm <image>`|
|Name container|`docker run --name <name> <image>`|
|Run with shell|`docker run -it <image> /bin/bash`|
|Run with entrypoint|`docker run --entrypoint <cmd> <image>`|

---

## 📊 Container Management

|Task|Command|
|---|---|
|List running containers|`docker ps`|
|List all containers|`docker ps -a`|
|Stop container|`docker stop <name/id>`|
|Start container|`docker start <name/id>`|
|Restart container|`docker restart <name/id>`|
|Remove container|`docker rm <name/id>`|
|Remove all stopped|`docker container prune`|

---

## 🌍 Networking

|Task|Command|
|---|---|
|Expose port|`docker run -p 8080:80 <image>`|
|View container IP|`docker inspect|
|List networks|`docker network ls`|
|Create network|`docker network create <name>`|
|Run on network|`docker run --network <name> <image>`|
|Connect container to net|`docker network connect <net> <container>`|

---

## 🗂️ Volumes & Mounts

|Task|Command|
|---|---|
|Create volume|`docker volume create <name>`|
|List volumes|`docker volume ls`|
|Run with volume|`docker run -v <vol>:<path> <image>`|
|Mount local folder|`docker run -v $(pwd):/app <image>`|
|Inspect volume|`docker volume inspect <name>`|

---

## 🐛 Logs, Exec & Inspection

|Task|Command|
|---|---|
|Container logs|`docker logs <name/id>`|
|Follow logs|`docker logs -f <name/id>`|
|Exec into container|`docker exec -it <name> /bin/bash`|
|Inspect container|`docker inspect <name>`|
|Resource usage|`docker stats`|

---

## 🧱 Dockerfile Essentials

```Dockerfile
FROM ubuntu:20.04
RUN apt update && apt install -y curl
COPY . /app
WORKDIR /app
CMD ["./start.sh"]
```

Build it:

```bash
docker build -t myapp .
```

---

## 🧼 Cleanup

|Task|Command|
|---|---|
|Remove all stopped containers|`docker container prune`|
|Remove all images|`docker rmi $(docker images -q)`|
|Remove all volumes|`docker volume prune`|
|System-wide cleanup|`docker system prune -a`|

---

## 🛠 Real-World Examples

|Purpose|Command|
|---|---|
|Run Kali Linux|`docker run -it kalilinux/kali-rolling /bin/bash`|
|Run NGINX on port 8080|`docker run -d -p 8080:80 nginx`|
|Persistent Ubuntu shell|`docker run -it --name myubuntu ubuntu`|
|Python app in mounted dir|`docker run -v $(pwd):/code python`|

---

