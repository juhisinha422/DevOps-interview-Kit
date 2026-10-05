Reverse Proxy
=============

A server that sits infront of one or more web servers and communicate between web servers and the internet. The reverse proxy server receives the request before sending it on to the internet resource for the client. After sending the request to one of the web servers, the reverse proxy receives the response from that server. The response is then sent back to the client by the reverse proxy.

--> Nginx is used as reverse proxy server.

--> server is aware of using proxy, client is not aware that server is using Nginx.

Reverse proxy is used for ?
========================

--> Backend applications are behind reverse proxy servers for security and queing.

--> Used in cache servers.

--> To hide server identity.

--> Acts as load balancer


![Image](https://github.com/user-attachments/assets/ffe3128c-4624-4941-a549-72ace3a6f66e)
