**References:**
- Local https enabled application tutorial: https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/
- Verify signed certificate using root CA cert tutorial: https://www.keenformatics.com/manually-verifying-an-ssl-certificate/
- Build custom Nginx Docker container: https://www.docker.com/blog/how-to-use-the-official-nginx-docker-image/
- HTTPS in Nginx: https://nginx.org/en/docs/http/configuring_https_servers.html
- Nginx redirect http to https: https://serverfault.com/a/298803
- Nginx set from which directory to serve static content: https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/

# Description
This project is intended to demonstrate the steps involved in an SSL handshake and demonstrate why it is cryptographically secure.
To do this, instructions and code to accomplish two tasks are included:
- Create a https enabled web server on your local machine which serves up static content using Nginx
- Verify a website's SSL certificate using both manually and OpenSSL CLI tool

## Create a https enabled web server on your local machine which serves up static content using Nginx
We will create two local web servers and run them using Docker. Each web server will have its own, different SSL certificate.
We will install only one of the root certificates used to sign these SSL certificates in our browser. We will then compare our
browser's behaviour when navigating to each web server.
- Instructions for creating a trusted HTTPS web server is found in `install-cert-in-browser/`
- Instructions for creating a non-trusted HTTPS web server is found in `dont-install-cert-in-browser/`

## Verify a website's SSL certificate using both Python standard library and OpenSSL CLI tool
There are four important files involved in setting up https for a web server.
1. Web server private key (used to decrypt encrypted date sent from client using web server public key)
2. Web server SSL cert (contains web server public key used for encrypting data sent from client to web server)
3. Certificate Authority private key (used to sign web server SSL certs)
4. Certificate Authority root certificate (verifies an SSL cert sent to a client was signed by the Certificate Authority's private key)
  - Typically installed in a trusted manner via your Operating System

To verify an SSL cert belongs to the website it claims to belong to:
1. Decrypt the signature attached to the web server SSL cert using the CA root certificate
2. Hash the data content of the SSL cert using the algorithm specified in the SSL cert
3. Ensure your hash equals the decrypted signature. If yes, the SSL cert is valid

To begin encrypted communication with a server after verifying its SSL cert:
1. Grab the web server's public key which is included in the SSL cert
2. Create a premaster secret which will be used to create a **symmetric** key
3. Encrypt your premaster secret using the server's public key and send it to them
4. Only the receiving web server can decrypt this premaster secret
5. You and the server both generate the same symmetric key
6. Encrypt all HTTP requests before being sent and decrypt all HTTP requests after being received using this symmetric key

We are just going to do the initial verification part of this process (aka the TLS/SSL handshake).
