Real-Time Production Example: Container running but app not reachable
----------------------------------------------------------------------------------
In one of our production microservices deployments, a container was in Running state, but users couldn’t access the application through the load balancer. We first checked the container logs and saw the application had started successfully, so the issue wasn’t a crash. Then we verified the port mapping and discovered the application was listening on port 8080 inside the container, but the Docker run command mapped host port 80 to container port 80 instead of 8080. After correcting the port mapping and redeploying the container, the application became reachable immediately without any code changes


![Image](https://github.com/user-attachments/assets/78b1f41a-6ba5-47c3-b529-303600e991ce)


![Image](https://github.com/user-attachments/assets/f051fa22-3a42-4475-a6dc-96157a719985)

![Image](https://github.com/user-attachments/assets/26271d8b-170d-4a73-b510-58f233cc9802)
