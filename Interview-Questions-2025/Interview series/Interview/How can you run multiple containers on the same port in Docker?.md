# How can you run multiple containers on the same port in Docker?

* Multiple containers **cannot bind the same host port directly**.

Example:

```bash id="dockport1"
docker run -p 80:80 nginx
```

If another container also tries:

```bash id="dockport2"
docker run -p 80:80 apache
```

* It will fail because host port `80` is already occupied.

---

## Important Clarification

Containers can internally use the same container port without issue.

Example:

* Container A → internal port 8080
* Container B → internal port 8080

This works because each container has its own network namespace.

Problem occurs only when binding same host port.

---

## Real Interview Expectation

Usually interviewer means:

> "How do you route traffic to multiple containers running on same server without exposing each container individually?"

---

## Correct Production Approach

Use:

* Reverse Proxy
* Load Balancer

Examples:

* Nginx
* Traefik
* HAProxy

---

## Example Flow

```text id="dockflow1"
User Request → Nginx Reverse Proxy → Container A / Container B
```

Example:

* app1.example.com → Container A
* app2.example.com → Container B

Both containers may internally run on:

```text id="dockflow2"
Container A → 8080
Container B → 8080
```

But only Nginx exposes:

```text id="dockflow3"
Host Port 80/443
```

---

## Docker Compose Example

```yaml id="dockcompose1"
services:
  nginx:
    image: nginx
    ports:
      - "80:80"

  app1:
    image: app1

  app2:
    image: app2
```

* Nginx routes traffic internally using Docker network.

---

## Best Interview Answer

"Containers can use same internal ports because they are isolated.
But multiple containers cannot bind the same host port directly.
In production we use reverse proxies/load balancers like Nginx or Traefik to route requests to multiple containers running on the same host."

---
