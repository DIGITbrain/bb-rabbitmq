# IMAGE SOURCE

Official image on __Docker Hub__:  https://hub.docker.com/_/rabbitmq

# Licence

Mozilla Public License v2.0

# Version

3.8.14

# DESCRPTION

RabbitMQ is open source message broker software (sometimes called message-oriented middleware) that implements the Advanced Message Queuing Protocol (AMQP). The RabbitMQ server is written in the Erlang programming language and is built on the Open Telecom Platform framework for clustering and failover. Client libraries to interface with the broker are available for all major programming languages.

# DEPLOYMENT

Example:

> docker run -d --rm --name rabbit -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=p4ss rabbitmq:3.8.14

With __"management plugin"__ opened on port 15672 (with pass: "user"/"guest"):

> docker run -d --name rabbit -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=p4ss __-p 15672:15672__ rabbitmq:__3.8.14-management__

PORTS:

        -p 5672:5672
        management GUI: -p 15672:15672


VOLUMES:

        -v $HOME/rabbitdata/:/var/lib/rabbitmq/

__Note__: the actual data dir get postfix: __rabbit@hostname__: e.g. /var/lib/rabbitmq/mnesia/rabbit@6299f74553ca

For additional configuration:

> /etc/rabbitmq/rabbitmq.conf

See: https://www.rabbitmq.com/configure.html#configuration-files



## TEST:

> Browser: http://host:15672


# AUTHENTICATION

Use:
```text
-e RABBITMQ_DEFAULT_USER=user 
-e RABBITMQ_DEFAULT_PASS=password 
```

## TEST

> Browser: http://host:15672, set: user/password.

# TLS

__Note__:

        SSL port: 5671 (listeners.ssl.default)
        SSL port management GUI: 15672 (listeners.ssl.default)

For details see: https://www.rabbitmq.com/ssl.html.

Create server certificates (ca.crt, server.key, server.crt):

```text
mkdir certs
openssl genrsa -out certs/ca.key 4096
openssl req -x509 -new -nodes -sha256 -key certs/ca.key -days 3650 -subj '/O=RabbitMQ Test/CN=Certificate Authority' -out certs/ca.crt
openssl genrsa -out certs/server.key 2048
openssl req -new -sha256 -key certs/server.key -subj '/O=RabbitMQ/CN=Server' | openssl x509 -req -sha256 -CA certs/ca.crt -CAkey certs/ca.key -CAserial certs/ca.txt -CAcreateserial -days 365 -out certs/server.crt
```

Use environment variables:

```text
RABBITMQ_SSL_CACERTFILE
RABBITMQ_SSL_CERTFILE
RABBITMQ_SSL_KEYFILE
```

## Run: 

> docker run -d --rm --name rabbit __-v $PWD/certs:/etc/ssl/certs/rabbit -e RABBITMQ_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt -e RABBITMQ_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt -e RABBITMQ_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key__ -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=p4ss -p __5671:5671__ rabbitmq:3.8.14

## SSL with __management plugin__:

```text
RABBITMQ_MANAGEMENT_SSL_CACERTFILE
RABBITMQ_MANAGEMENT_SSL_CERTFILE
RABBITMQ_MANAGEMENT_SSL_KEYFILE
```

## Run:
> docker run -d --rm --name rabbit __-v $PWD/certs:/etc/ssl/certs/rabbit -e RABBITMQ_MANAGEMENT_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt -e RABBITMQ_MANAGEMENT_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt -e RABBITMQ_MANAGEMENT_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key__ -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=p4ss __-p 15671:15671__ rabbitmq:3.8.14-management

## TEST

> Browser: https://host:15671, set: user/p4ss.


# TLS mutual authentication

Use environment variables: 

        -e RABBITMQ_SSL_VERIFY=true
        -e RABBITMQ_SSL_FAIL_IF_NO_PEER_CERT=true

        management GUI: -e RABBITMQ_MANAGEMENT_SSL_VERIFY=true
        management GUI: -e RABBITMQ_MANAGEMENT_SSL_FAIL_IF_NO_PEER_CERT=true

## Example:

> docker run -d --rm --name rabbit -v $PWD/certs:/etc/ssl/certs/rabbit __-e RABBITMQ_MANAGEMENT_SSL_VERIFY=verify_peer -e RABBITMQ_MANAGEMENT_SSL_FAIL_IF_NO_PEER_CERT=true__ -e RABBITMQ_MANAGEMENT_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt -e RABBITMQ_MANAGEMENT_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt -e RABBITMQ_MANAGEMENT_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key -e RABBITMQ_DEFAULT_USER=user -e RABBITMQ_DEFAULT_PASS=p4ss -p 15671:15671 rabbitmq:3.8.14-management
