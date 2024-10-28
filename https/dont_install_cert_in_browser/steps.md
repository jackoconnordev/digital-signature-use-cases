# Description

**NOTE: THIS IS A REPEAT OF THE STEPS FOUND IN `install_cert_in_browser`. WHEN CREATING
THIS SERVER DO NOT INSTALL OUR CUSTOM CA ROOT CERTIFICATE TO DEMONSTRATE THAT OUR
BROWSER WILL REJECT THE HTTPS CONNECTION.**

Run these steps manually in order to create and run a local https enabled Nginx server.

## Create certs

Create private key for our own local Certificate Authority. I'm using password == "password".
```sh
mkdir ca-certs
openssl genrsa -des3 -out ca-certs/myCA_private.key 2048
```
Create root certificate for our own local Certificate Authority. It doesn't matter which values
are used in the prompt when creating the root certificate for our purposes.
Once again I'm using password == "password".
```sh
openssl req -x509 -new -nodes -key ca-certs/myCA_private.key -sha256 -days 1825 -out ca-certs/myCA_public_dont_install.pem
```
We also need to make a private key for our server and to digitally sign its corresponding public key
using our local Certificate Authority's private key.
Create the private key.
```sh
mkdir server-certs
openssl genrsa -out server-certs/myServer_private.key 2048
```
Create a Certificate Signing Request (CSR) file based on our private key. Again for our
purposes the values we use to fill in the prompt do not matter.
Leave the 'extra' attributes at the end of the prompting blank.
```sh
openssl req -new -key server-certs/myServer_private.key -out server-certs/myServer.csr
```
There is a file `x509_v3_extension.ext` in this directory which we will use
to configure `openssl` to make our server's signed SSL cert valid for the domain
`httpsserver.local`.

Finally, we will create the server's digitally signed public SSL cert using:
- the server's certificate signing request
- the certificate authority's private key
- the certificate authority's root certificate
- the `x509_v3_extension.ext` file

```sh
openssl x509 -req -in server-certs/myServer.csr -CA ca-certs/myCA_public_dont_install.pem -CAkey ca-certs/myCA_private.key \
-CAcreateserial -out server-certs/myServer_signed.crt -days 825 -sha256 -extfile x509_v3_extension.ext
```
Final results of the "Create certs" step:
- ca-certs/myCA_private.key
- ca-certs/myCA_public_dont_install.pem
- ca-certs/myCA_public.srl (ignore this / Google at your own leisure)
- server-certs/myServer.csr
- myServer_private.key
- myServer_signed.crt

Until you install `ca-certs/myCA_public_dont_install.pem` as a root certificate either in your browser or
in your operating system, our browser will not allow HTTPS connections to our Nginx server whose
SSL cert was signed by `ca-certs/myCA_private.key`.

## Build Nginx Docker container

We will use the Nginx base image from Dockerhub to do most of the heavy lifting.
To customise the built container we will copy in our own:
- `nginx.conf` file to override the Nginx server's default settings
- our server's private key and digitally signed SSL cert to enable https configuration
- `index.html` file to have content to serve to our browser

```sh
docker build -t https-server-not-installed-cert .
```

## Run Nginx Docker container

On your system append to your `/etc/hosts` file so `httpsserver.local` points to our Nginx server.

```
127.0.0.1       httpsserver.local
```

Then to start the https server locally run
```sh
docker run -it --rm -d -p 443:443 -p 80:80 --name nginx-https-invalid-cert https-server-not-installed-cert
```
