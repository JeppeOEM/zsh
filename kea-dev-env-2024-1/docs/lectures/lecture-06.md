# Lecture 6: Virtualization and Process Isolation - Part II

Last lecture we started looking at Docker - we will continue with Docker in this lecture, but we will be using a tool call `docker-compose` that will allow us to work with Docker in a much better way.

A `Dockerfile` describes how to build a Docker image, but we then have to specify *how* to run the container with lots of command line arguments.
That is not very practical.
However, that is one of the problems we solve by using `docker-compose`.


## Docker-Compose

When using `docker-compose` we can use commands like:

- `docker-compose up` to run one or more containers with their settings for network ports, volumes (file systems), environment variables, start up order, etc.
- `docker-compose up -d` to run in the background.
- `docker-compose down` to bring down all the Docker containers.

But before that, we need a `docker-compose.yml` file.

The best way to learn a tool is to see how it works, so let's go through some examples to see how `docker-compose` works.

## Running A Simple Container

```yaml title="docker-compose.yml"
version: '3'

services:
  nginx:
    image: nginx:latest
    ports:
      - 8000:80
```

We start be specifying the file version.
`docker-compose` has evolved over the years, and there are several versions of the file format.

Next we define a `service` named `nginx`.
A service in a `docker-compose` file describes how to run a Docker container.

The service will use the `nginx:latest` image, and expose the container's port `80` to the host machine's port `8000`.

Let's try to run the file:

```bash
henrik@kea-dev:~/try-docker/nginx-1 $ docker-compose up -d

Building with native build. Learn about native build in Compose here: https://docs.docker.com/go/compose-native-build/
Creating network "nginx-1_default" with the default driver
Creating nginx-1_nginx_1 ... done

henrik@kea-dev:~/try-docker/nginx-1 $ http 127.0.0.1:8000

HTTP/1.1 200 OK
Accept-Ranges: bytes
Connection: keep-alive
Content-Length: 612
Content-Type: text/html
Date: Wed, 03 Mar 2021 07:48:10 GMT
ETag: "602beb5e-264"
Last-Modified: Tue, 16 Feb 2021 15:57:18 GMT
Server: nginx/1.19.7

<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
...
</head>
    <body>
        <h1>Welcome to nginx!</h1>
...
    </body>
</html>
```

We will see the standard Nginx welcome message.
This is because we have not configured Nginx in any way.


## Serving Static Files

The first example ran the Nginx web server, but with the default content.
That's not very useful, so let's have it share our own content:

!!! note inline end

    Using the syntax `./html:` indicates a local path in the current directory `.`.
    This is called a *bind-mount*.

```yaml title="docker-compose.yml"
version: '3'

services:
  nginx:
    image: nginx:latest
    ports:
      - 8000:80
    volumes:
      - ./html:/usr/share/nginx/html:ro
```

This time we define a volume `./html` that points to `/usr/share/nginx/html` inside the Docker container, and we set the `ro` flag for `read-only`.

The `./html` folder is where we put our content on the host machine, and by default Nginx will look for a file named `index.html`.
But how did we know to map it to `/usr/share/nginx/html/`?
That is something you will have to find out from the documentation.
By default, images are pulled from `https://hub.docker.com`, and this is also where you will find the information about each and every image you will be using, that you did not write yourself.

We can change the content of the `index.html` file, and it will dynamically update on the running container.
This is because we pointed to a location in the host file system, rather than copying files.


## Running Several Services

Even when running just a single container `docker-compose` is nice, because we can put all the run-time parameters in the `docker-compose` file rather than having to enter them or write custom shell scripts.

But the real power comes when we have a whole composition of Docker containers that encompasses the whole system.
Let's look at an example:

```yaml title="docker-compose.yml" linenums="1"
version: '3'

services:

  database1:
    image: postgres:10
    volumes:
      - database1_volume:/var/lib/postgresql/data
    env_file:
      - config/db/database1_env
    networks:
      - database1_network

  database2:
    image: postgres:10
    volumes:
      - database2_volume:/var/lib/postgresql/data
    env_file:
      - config/db/database2_env
    networks:
      - database2_network

  djangoapp:
    build: .
    environment:
      - DJANGO_SETTINGS_MODULE
    volumes:
      - .:/opt/services/djangoapp/src
      - static:/opt/services/djangoapp/static
      - media:/opt/services/djangoapp/media
    networks:
      - database1_network
      - database2_network
      - nginx_network
    depends_on:
      - database1
      - database2

  nginx:
    image: nginx:latest
    ports:
      - 8000:80
    volumes:
      - ./config/nginx/conf.d:/etc/nginx/conf.d
      - static:/opt/services/djangoapp/static
      - media:/opt/services/djangoapp/media
    networks:
      - nginx_network
    depends_on:
      - djangoapp

networks:
  database1_network:
    driver: bridge
  database2_network:
    driver: bridge
  nginx_network:
    driver: bridge

volumes:
  database1_volume:
  database2_volume:
  static:
  media:
```

There's a lot going on here, but let's just take it one item at a time.

First of all, we see that the top-most level now has `services`, `networks`, and `volumes`.

The `service` level describes four services: two databases, an application server, and a web server.
But before we go into details with the services, let's first look at networks and volumes.

The `networks` level describes three separate networks: one for each database, and one for the web server.
This separation is primarily done for security reasons: even if you could take over the web server (the one facing the internet directly), you would not have network access to the databases.

You might wonder where the network for the application server is?
Well, the application server connects to all three networks, as it needs to communicate with all of the other servers, so it doesn't need its own network.

The `volumes` level describes four volumes: two database volumes, a volume named `static` for static files, and a volume named `media` for uploaded files.
When we define volumes this way we get meaningful names for them, and they will persist when we take the composition down, something that is not the case if we save data within the container (remember last week's lecture: when we run a container we get a fresh copy of the image).

!!! note inline end

    We typically use *bind-mounts* for source files that we want to manipulate directly during development.
    Files in bind-mounts are typically copied into the image in the `Dockerfile`, so the project directory is not needed to run the container.
    Using a bind-mount this way eliminates the need to constantly rebuild the Docker image.

    *Named volumes* are typically used for database files and other app content that we do not need access to during development.

We have already seen the bind-mounts, the volumes that binds to a local path using `./` on the left side of the `:`.
Here we see another type of volumes: the *named volumes*.
Named volumes simply have a name on the left side of the `:`, e.g., `static`, `media`, `database1_volume`.
You will not find the files from these volumes in your project directory.
These volumes are managed by Docker itself, and you should not manipulate them directly, but rather start a container mounting the particular volume.
You can use the `docker-compose volume inspect` command to see details for the particular volume.
Named volumes must also be listed in the `volumes` section.

Finally, let's get back to the `services`.

The two databases are nearly identical, but notice that we use named volumes for the databases.

Similarly, we connect to the network that is named in the `network` level, so no surprise there.

The `env_file` requires some explanation: the PostgreSQL database we are running requires information such as the name of the database, the admin user name, and the admin password.
We don't want to put such information under version control (at least for production), so putting this information in its own file makes this easier.

The `env_file` looks something like this:

```bash title="env_file"
DEBUG=1
SECRET_KEY=foo
DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1 [::1]
SQL_ENGINE=django.db.backends.postgresql
POSTGRES_DB=movies_dev
POSTGRES_USER=movies
POSTGRES_PASSWORD=movies
SQL_HOST=movies-db
SQL_PORT=5432
```

The three lines beginning with `POSTGRES_` holds the information we need for the database.
The rest are settings for Django that should probably be in its own file.

The `djangoapp` service is our application server.
We are not going into details with Django, but we can see that this container is build from a custom image, we specify the source directory in the file system as well as two named volumes (`static` and `media`).

We also connect to all three networks as described earlier.

The `Dockerfile` for the `djangoapp` service looks like this:

!!! tip inline end

    If you look closely you will see that we copy the `requirements.txt` file twice.
    The environment files will probably take up much more space than our project files, but also change much more frequently.
    Having them in a layer above the source files means we will likely be able to better utilize the cache.

```Dockerfile title="Dockerfile"
FROM python:3.9-alpine

ENV PYTHONBUFFERED 1
RUN mkdir -p /opt/services/djangoapp/src

COPY requirements.txt /opt/services/djangoapp/src/.
WORKDIR /opt/services/djangoapp/src/hello/
RUN pip install --upgrade pip && pip install -r ../requirements.txt

COPY . /opt/services/djangoapp/src

COPY ./entrypoint.sh /
ENTRYPOINT ["sh", "/entrypoint.sh"]
```

The `ENTRYPOINT` runs an external script, that will start a Gunicorn WSGI server.
This is specific to Python based application servers, but for completeness sake the file looks like this:

```bash title="entrypoint.sh"
#!/bin/sh

python manage.py migrate --no-input
python manage.py collectstatic --no-input

gunicorn hello.wsgi:application --bind :8000
```

Other development ecosystems have similar ways of running their applications, but they are specific to programming language and/or framework.

Finally, we have the `depends_on` setting: the two databases must be up and running before the application server start up, so making this service depend on the two database services ensures that this service will wait starting up until they are online and ready.

Last, we have the `nginx` service.
This time we provide a custom configuration for Nginx.
We do this by knowing that Nginx will look for its configuration file in `/etc/nginx/conf.d`.
By mapping a location in our local file system `./config/nginx/conf.d` we can provide a custom configuration file `nginx.conf`:

```nginx.conf title="nginx.conf"
upstream hello_server {
    server djangoapp:8000;
}

server {
    listen 80;
    server_name localhost;

    location /static/ {
        alias /opt/services/djangoapp/static/;
    }

    location /media/ {
        alias /opt/services/djangoapp/media/;
    }

    location / {
        proxy_pass http://hello_server;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;
        proxy_redirect off;
        if (!-f $request_filename) {
            proxy_pass http://hello_server;
            break;
        }
    }
}
```

The configuration file for Nginx describes an *upstream* server `hello_server`.
Note that we specify the service name `djangoapp` rather than an IP address.
This is due to the fact that `docker-compose` provides name resolution of services in Docker networks, and also that we don't actually know what IP address the container will get.


## Docker Networks

!!! note inline end

    You should always make separate networks for services.

As we have seen above, we can make separate networks for our services to communicate.

But what happens if you don't?
Well, Docker will connect your services to the *Default Docker Network*.
Because containers are supposed to be portable, you should never assume that you know what else might be running on the default network, and thus results can be unpredictable.

You should also be careful which services expose ports outside their service networks.
In the example above, only the `nginx` service expose a port to the host machine (line 42).
There is no need for other services to expose ports, and doing so will in fact pose a security risk.


## Docker Host To Host Communication In Separate Projects

Say we have two projects: `project1` and `project2`.

The two projects each have their own nginxi (`web`), app (`app`), and PostgreSQL (`db`) services.

The `app` service is based on a custom `Dockerfile`:

```yaml title="Dockerfile"
FROM python:3.11-alpine

ENV PYTHONUNBUFFERED=1

COPY . /app/
WORKDIR /app

RUN pip install -r requirements.txt

ENTRYPOINT ["python","manage.py", "runserver"]
```

The `docker-compose.yml` file for `project1`:

```yaml linenums="1" title="docker-compose.yml" hl_lines="29"
version: '3.5'

services:

  db:
    image: postgres:14
    networks:
      - dbnet
    env_file:
      - env

  app:
    image: h2h
    networks:
      - appnet
      - dbnet

  web:
    image: nginx:latest
    ports:
      - 8000:80
    networks:
      - appnet
      - webnet

networks:
  webnet:
    driver: bridge
    name: b2b
  appnet:
    driver: bridge
  dbnet:
    driver: bridge
```

The `webnet` is given a name `b2b` that is used to access it from `project2`.

The `docker-compose.yml` file for `project2`:

```yaml linenums="1" title="docker-compose.yml" hl_lines="26 36-38"
version: '3.5'

services:

  db:
    image: postgres:14
    networks:
      - dbnet
    env_file:
      - env

  app:
    image: h2h
    networks:
      - appnet
      - dbnet
      - proxynet

  web:
    image: nginx:latest
    ports:
      - 4000:80
    networks:
      - appnet
      - webnet
      - proxynet

networks:
  webnet:
    driver: bridge
    name: b2b
  appnet:
    driver: bridge
  dbnet:
    driver: bridge
  proxynet:
    name: b2b
    external: true
```

The `proxynet` is also given the name `b2b`.

If we now look at the `b2b` network from the perspective of `project2`:

```bash
docker network inspect b2b
```

We get some interesting output:

```json hl_lines="21"
[
    {
        "Name": "b2b",
...
        "Containers": {
            "427c2f7d903714596e18a594c2912415fd30dadb4dd6b0a17a0cb35280d854ab": {
                "Name": "project2-app-1",
                "EndpointID": "dd7ea0f51953eed758e371444ca04b2e8d5008ce1027c5ff7b6a33553c25af20",
                "MacAddress": "02:42:ac:1a:00:04",
                "IPv4Address": "172.26.0.4/16",
                "IPv6Address": ""
            },
            "4b86b7fea6469aae7c9e7f25eb57617a91bd066b3571bcca097a40fe886bf5e7": {
                "Name": "project2-web-1",
                "EndpointID": "d5f5f87c8bb44deb6d97d90f1ede565091ddb3ee066d587f249befb04ecc12f1",
                "MacAddress": "02:42:ac:1a:00:03",
                "IPv4Address": "172.26.0.3/16",
                "IPv6Address": ""
            },
            "ad1243d89515e5f93d55f9e0d7bd04f936637b3e2097aba1b886132886a93fbb": {
                "Name": "project1-web-1",
                "EndpointID": "146c1f5a08c5a3c71793284a3327218785bcc904741883e62d5a285195ef7462",
                "MacAddress": "02:42:ac:1a:00:02",
                "IPv4Address": "172.26.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {
            "com.docker.compose.network": "webnet",
            "com.docker.compose.project": "project1",
            "com.docker.compose.version": "2.17.3"
        }
    }
]
```

We can see the `web` service from `project1` named `project1-web-1`.
This name can be used to access `project1` from `project2`.

```bash
docker-compose exec app sh
```

And then:

```bash
apk add httpie
http project1-web-1
```

This will produce the HTTP response from the `project1` web service.


## Assignments

#### Assignment 1:

- Complete the tutorial from Part I of this lecture.


#### Assignment 2:

Make a requirement specification for a process isolation system.


## Video Lectures - Containerize your app

The following videos go through the complete process of containerizing a web application.

- [Part 1](https://youtu.be/vSFQuoiefwU)
- [Part 2](https://youtu.be/2IJoPH56oJg)
- [Part 3](https://youtu.be/qfdQxk-u1zs)
- [Part 4](https://youtu.be/a9xlOODrmgA)
- [Part 5](https://youtu.be/RMbwF3SVF4E)
- [Part 6](https://youtu.be/dZhuLC-hn-s)
- [Part 7](https://youtu.be/UV9NrvafebI)
- [Part 8](https://youtu.be/iOwX2IATVBs)

