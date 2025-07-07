Day 13:  🔁 API Gateway vs Load Balancer (ALB) –

What’s the Difference?

These two services often confuse beginners because both handle incoming traffic—but they serve very different purposes.

🔹 API Gateway is a fully managed service to create, publish, and manage APIs (REST, HTTP, WebSocket). It’s perfect for serverless apps and microservices, offering features like authentication, throttling, caching, and request/response transformation. It integrates well with Lambda, Step Functions, and DynamoDB, and charges per API call and data transfer.

🔹 Application Load Balancer (ALB) is used to distribute HTTP/HTTPS traffic across EC2, ECS, or Lambda targets. It supports path-based and host-based routing, integrates with Security Groups and WAF, and is ideal for web apps and containerized services. Pricing is based on uptime and data processed.

✅ Use API Gateway for API management in serverless architectures. Use ALB for traffic distribution across backend services.
✨ Think of API Gateway as the API manager, and ALB as the traffic router.

✅ When to Use What?

Use API Gateway for managing APIs with authentication, request/response transformation, and tight integration with serverless apps.

Use ALB for distributing traffic to web apps, EC2s, or containers with path-based or host-based routing.
✨ Think of API Gateway as the API manager, and ALB as the traffic director for your application backend.



![Image](https://github.com/user-attachments/assets/903c43d8-4cd3-4843-b08d-dd4a08ddd610)
