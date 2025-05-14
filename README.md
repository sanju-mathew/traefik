# 🧭 Traefik Proxy in a Homelab Environment

## 🔍 Overview

This guide explains how to set up **Traefik Proxy** in a homelab environment, replacing Nginx Proxy Manager. Traefik is a dynamic reverse proxy that integrates deeply with Docker and Kubernetes, offering built-in support for SSL, observability, and scalability.

### Why Traefik?
- **Dynamic Configuration**: Updates automatically with changes in containers/services
- **Docker/K8s Native**: Detects containers and adjusts routing based on labels
- **Built-in SSL**: Let's Encrypt support via HTTP-01 and DNS-01
- **Observability**: Includes a dashboard, metrics, and tracing
- **Performance**: Efficient for high-scale microservices setups

## 🔁 Reverse Proxy Concept

A **reverse proxy** acts as a gateway between external users and internal services:
- Protects internal apps
- Enables HTTPS
- Routes requests to correct containers/services
- Simplifies domain handling and access control

## ⚖️ Nginx Proxy Manager vs. Traefik

| Feature             | Nginx Proxy Manager      | Traefik                            |
|---------------------|--------------------------|------------------------------------|
| Core                | Nginx                    | Go-based dynamic proxy             |
| UI                  | Web UI                   | Dashboard only                     |
| SSL                 | Auto via UI              | Auto via DNS challenge             |
| Docker Integration  | Basic                    | Excellent via labels               |
| Routing             | Manual                   | Dynamic rule-based                 |
| Load Balancing      | Basic                    | Advanced (sticky sessions, etc.)   |
| Setup Difficulty    | Easy                     | Intermediate                       |

## 🛠️ Docker Compose Example

```yaml
secrets:
  cf-token:
    file: ./cf-token

services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: unless-stopped
    security_opt:
      - no-new-privileges:true
    secrets:
      - cf-token
    env_file:
      - .env
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
    environment:
      - TRAEFIK_DASHBOARD_CREDENTIALS=${TRAEFIK_DASHBOARD_CREDENTIALS}
      - CF_API_EMAIL=youremail@example.com
      - CF_DNS_API_TOKEN_FILE=/run/secrets/cf-token

networks:
  proxy:
    external: true
