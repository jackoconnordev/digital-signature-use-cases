**References:**
- Local https enabled application tutorial: https://deliciousbrains.com/ssl-certificate-authority-for-local-https-development/
- Verify signed certificate using root CA cert tutorial: https://www.keenformatics.com/manually-verifying-an-ssl-certificate/
- Nginx redirect http to https: https://serverfault.com/a/298803
- Build custom Nginx Docker container: https://www.docker.com/blog/how-to-use-the-official-nginx-docker-image/
- HTTPS in Nginx: https://nginx.org/en/docs/http/configuring_https_servers.html
- Nginx set directory to serve static content from: https://docs.nginx.com/nginx/admin-guide/web-server/serving-static-content/

# Description
This project is intended to demonstrate the steps involved in an SSL handshake and demonstrate why it is cryptographically secure.
To do this, instructions and code to accomplish two tasks are included:
- Create a https enabled web server on your local machine which serves up static content using Nginx
- Verify a website's SSL certificate using both manually and OpenSSL CLI tool

## Create a https enabled web server on your local machine which serves up static content using Nginx
We will create two local web servers and run them using Docker. Each web server will have its own, different SSL certificate.
We will install only one of the root certificates used to sign these SSL certificates in our browser. We will then compare our
browser's behaviour when navigating to each web server.

## Verify a website's SSL certificate using both Python standard library and OpenSSL CLI tool
There are four important files involved in setting up https for a web server.
1. Web server private key (used to decrypt encrypted date sent from client using web server public key)
2. Web server SSL cert (contains web server public key used for encrypting data sent from client to web server)
3. Certificate Authority private key (used to sign web server SSL certs)
4. Root CA certificate (verifies an SSL cert sent to a client was signed by the Certificate Authority's private key)
