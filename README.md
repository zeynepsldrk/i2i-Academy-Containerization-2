# i2i-Academy-Containerization-2
This project, developed as part of the i2i Systems summer internship, involves containerizing a simple static web page using Docker and Docker Compose and deploying it both in a local environment and on a Google Cloud Platform (GCP) Virtual Machine (VM).

## Project Scope

- A simple static web page containing a custom message was created using the `index.html` file.
- The `docker-compose.yml` file defines the service that will serve this page using the official `nginx:latest` image.
- The container was first run on the local machine and verified via `localhost`, then the same setup was replicated on a GCP Compute Engine VM (Debian Linux), making it accessible via the VM’s public IP address.

## Technologies Used

- Docker & Docker Compose
- Nginx (nginx:latest image)
- Google Cloud Platform - Compute Engine

## Running

```bash
docker compose up -d
```

Locally: `http://localhost:8080`
On the Cloud VM: `http://<VM_PUBLIC_IP>:8080` (the firewall rule must keep the relevant port open)

## Learning Reference

This section summarizes the conceptual knowledge gained while completing the project.

**Why was Docker Compose chosen?**
Instead of starting a single service with `docker run` using long parameters, defining the entire configuration (image, port mapping, volume mounting) in the `docker-compose.yml` file makes the command repeatable and allows the same file to be transferred exactly as-is to different environments (such as a Cloud VM).

**Volume Mount Logic**
Instead of being copied into the container, the `index.html` file is mounted via `volumes` to the `/usr/share/nginx/html` directory inside the container. This ensures that changes made to the file take effect immediately without the need to restart the container; copying the file would not provide this live connection.

**Port mapping**
In the `8080:80` configuration, the value on the left specifies the host machine’s port, while the value on the right specifies the port that Nginx is listening on inside the container. Requests coming to port 8080 on the host machine are redirected by Docker to port 80 in the container.

**Elements That Change and Remain the Same When Moving from Local to the Cloud**
The container definition (`docker-compose.yml`) and the application file (`index.html`) remain exactly the same between the local environment and the Cloud VM; this is direct proof of the portability advantage of containerization. The only element that changed is the access address: while `localhost` is sufficient locally, the VM’s public IP address was used to access a service running on the VM, because `localhost` refers only to the machine from which the request is made.

**Firewall Experience**
Although the container ran successfully on the VM, the page could not be accessed via the public IP on the first attempt. This was not because the container was not running, but because GCP’s default firewall rules do not allow traffic to port 8080. Access was granted by adding a rule in the VPC Network > Firewall section to allow traffic on TCP port 8080.
