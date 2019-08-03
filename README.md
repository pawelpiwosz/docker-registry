## Dockerized Docker Registry

### Synopsis

Run your own docker registry with Nginx on front.

### Before start

Before you run the docker-compose stack, you need to prepare couple of things.  
* Create certificates for Nginx (tutorial below)  
* Create basic auth  
* Change defaults in Nginx configs to your domain.  

### Certificates for Nginx

In order to prepare TLS certificates, go through this mini tutorial:

__Create certificate pair__

```
sudo openssl req -x509 -nodes -days 3650 -newkey rsa:2048 -keyout nginx/ssl/nginx-selfsigned.key -out nginx/ssl/nginx-selfsigned.crt
```

__Create a Diffie-Hellman pair__

```
sudo openssl dhparam -out nginx/ssl/dhparam.pem 4096
```

Recommended by https://cipherli.st/ is 4096, but depending on the usecase, you can use lower value.

__Create self-signed config__

```
echo "ssl_certificate /etc/nginx/ssl/nginx-selfsigned.crt;" > nginx/ssl/self-signed.conf
echo "ssl_certificate_key /etc/nginx/ssl/nginx-selfsigned.key;" >> nginx/ssl/self-signed.conf
```

__Create ssl config__

You can use snippet from https://cipherli.st/, or prepared one from this repo.

### Configure auth for Registry

__Create password file for registry__

First, create `auth` directory in root of this project. Then run:

```
htpasswd -Bc registry/auth/.htpasswd registry-user
```

### Run stack

In order to run the stack, execute:

```
docker-compose run -d
```

or

```
docker-compose run -d --build
```

to rebuild the images

### Stop stack

In order to stop the stack, run:

```
docker-compose down
```

### Registry usage

First, you need to login to the registry:

```
docker login https://<DOMAIN>/v2 --username=<USER>
```

Next, tag your image:

```
docker image tag alpine:3.9 <DOMAIN>/alpine:3.9
```

And you are ready to push the image to local registry:

```
docker push <DOMAIN>/alpine:3.9
```

* <USER> - your user, defined in basic authorization  
* <DOMAIN> - domain configured in Nginx

__Important__ for domain you need to use at leas something like `somename.somedomain`. 
In other case, push command will try to connect to docker.io.

In order to check your registry, you can go to the browser and use the url:

```
<DOMAIN>/v2/_catalog
```

### Credits

I used official Docker image for Registry and for Nginx.

And a lot of Internet sources :)