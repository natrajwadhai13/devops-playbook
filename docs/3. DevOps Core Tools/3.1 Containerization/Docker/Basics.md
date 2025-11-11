---
title: "Docker Basics"
parent: "• Docker"
grand_parent: "• 3.1 Containerization"
great_grand_parent: "3. DevOps Core Tools"
nav_order: 1
---

# Docker Basics Command used in Project 

```bash
for Build 

docker build --no-cache -t backendcompliance-audit-be-main:9 .
docker run -d --name Live_backend_container -p 4000:4000 backendcompliance-audit-be-main:1

docker images
docker ps 
docker ps -a

docker rm 4c69337792ea
docker stop 4c69337792ea
docker logs 107480a1e80a
docker logs -f 107480a1e80a
docker exec -it 2ad2f22199fd /bin/bash



```