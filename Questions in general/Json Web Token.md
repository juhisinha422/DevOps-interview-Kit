What is a JSON Web Token (JWT)?

A JWT is a compact, URL-safe token used to securely transmit information between two parties, typically a client and a server.

It's widely used for authentication and authorization in modern web apps.

A JWT looks like this: xxxxx[dot]yyyyy[dot]zzzzz

It has 3 parts:

- Header: Defines the type of token and the signing algorithm (like HMAC or RSA)
  
- Payload: Carries the claims, user information (like userId, role)
  
- Signature: Used to verify that the sender. Ensures the token hasn’t been tampered with.

How does it work?
1. The user logs in with credentials.
2. The server validates credentials and generates a JWT 
3. The server sends the JWT back to the client
4. The client stores the JWT token (typically in localStorage or cookies)
5. For future requests, the client includes the JWT in the Authorization header.


![Image](https://github.com/user-attachments/assets/3ec22442-ca38-4146-964a-9bf6eaf3d375)
