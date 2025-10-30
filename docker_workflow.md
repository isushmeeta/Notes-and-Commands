# Docker Workflow Notes

## General Commands
```bash
docker --version                         # Check Docker version
docker info                              # Show system-wide information
docker ps                                # List running containers
docker ps -a                             # List all containers (running + stopped)
docker images                            # List all Docker images
docker volume ls                         # List all Docker volumes
docker network ls                        # List all Docker networks
```

## Building & Running Containers
```bash
docker build -t <image-name> .            # Build Docker image from Dockerfile
docker run -d --name <container-name> <image-name>   # Run container in detached mode
docker run -it <image-name> /bin/bash     # Run and open an interactive bash shell
docker exec -it <container-name> bash     # Enter an already running container
```

## Docker Compose Commands
```bash
docker compose up -d                      # Build and start all services in background
docker compose up --build                 # Rebuild images and start containers
docker compose down                       # Stop and remove containers, networks, volumes
docker compose ps                         # List containers managed by compose
docker compose logs -f                    # Stream logs from all services
docker compose exec <service> bash        # Open shell inside a running service container
```

## Stopping & Cleaning Up
```bash
docker stop <container-id>                # Stop a specific container
docker start <container-id>               # Start a stopped container
docker rm <container-id>                  # Remove container
docker rmi <image-id>                     # Remove image
docker system prune -a                    # Remove all stopped containers & unused images
docker volume prune                       # Remove unused volumes
```

## Copy Files Between Host and Container
```bash
docker cp <container-id>:/path/in/container /path/on/host
docker cp /path/on/host <container-id>:/path/in/container
```

## Viewing Logs & Processes
```bash
docker logs <container-name>              # Show logs for a specific container
docker logs -f <container-name>           # Follow logs (like tail -f)
docker top <container-name>               # Show running processes in a container
```

## Tagging & Pushing Images
```bash
docker tag <local-image> <username>/<repo>:<tag>    # Tag image
docker login                                        # Log in to Docker Hub
docker push <username>/<repo>:<tag>                 # Push image to Docker Hub
docker pull <username>/<repo>:<tag>                 # Pull image from Docker Hub
```

## Inspecting and Debugging
```bash
docker inspect <container-id>              # Detailed JSON info about a container
docker inspect <image-id>                  # Inspect image details
docker network inspect <network-name>      # Show network details
```

## Useful Tips
```bash
# Rebuild specific service in Docker Compose
docker compose up -d --build <service-name>

# Restart all services
docker compose restart

# Enter frontend container (for OmegaUp)
docker compose exec frontend bash

# Check environment variables inside container
printenv
```

## Common Cleanup Workflow
```bash
docker compose down
docker system prune -a
docker volume prune
docker compose up --build -d
```

## Summary Table
| Action | Command |
|--------|----------|
| Build image | `docker build -t <name> .` |
| Run container | `docker run -d --name <name> <image>` |
| Enter container | `docker exec -it <name> bash` |
| View logs | `docker logs -f <name>` |
| Stop all containers | `docker stop $(docker ps -aq)` |
| Remove all containers | `docker rm $(docker ps -aq)` |
| Remove all images | `docker rmi $(docker images -q)` |
| Clean up everything | `docker system prune -a` |