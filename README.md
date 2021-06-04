# General

## Deployment type

docker

## Image 

Based on official image on Docker Hub: https://hub.docker.com/_/rabbitmq

## Licence

Mozilla Public License v2.0

## Version

3.8.14

## Description

RabbitMQ is open source message broker software (sometimes called message-oriented middleware) that implements the Advanced Message Queuing Protocol (AMQP). The RabbitMQ server is written in the Erlang programming language and is built on the Open Telecom Platform framework for clustering and failover. Client libraries to interface with the broker are available for all major programming languages.

# Deployment

A. General example:

```sh
docker run -d --rm \
        --name rabbit \
        -e RABBITMQ_DEFAULT_USER=user \
        -e RABBITMQ_DEFAULT_PASS=p4ss \
        rabbitmq:3.8.14
```

B. With *management plugin* opened on port 15672 (with user/pass: *user* / *p4ss* ):

```sh
docker run -d \
        --name rabbit \
        -e RABBITMQ_DEFAULT_USER=user \
        -e RABBITMQ_DEFAULT_PASS=p4ss \
        -p 15672:15672 \
        rabbitmq:3.8.14-management
```

## Parameters

|Name|Value|Description|
|-|-|-|
|Ports|`-p 5672:5672`<br/>`-p 15672:15672`| Normal ports<br/> management GUI ports|
|Volumes|`-v $HOME/rabbitdata/:/var/lib/rabbitmq/`| |

For additional configuration edit `/etc/rabbitmq/rabbitmq.conf`. See [1] for more details.

## Testing

Direct your browser at: [http://\<HOST\>:15672](http://<HOST>:15672)

# Authentication

Use:
```sh
-e RABBITMQ_DEFAULT_USER=user
-e RABBITMQ_DEFAULT_PASS=password
```

## Testing

Direct your browser at: [http://\<HOST\>:15672](http://<HOST>:15672), set: user/password.

# TLS

|Port|Description|Configuration setting|
|-|-|-|
|5671| SSL port | `listeners.ssl.default` |
|15672| SSL port for management GUI | `listeners.ssl.default` |

For details see: https://www.rabbitmq.com/ssl.html.

To create server certificates (`ca.crt`, `server.key`, `server.crt`), execute the following, replace the subject (value of `-subj` parameter) as needed:

```sh
mkdir certs

openssl genrsa -out certs/ca.key 4096

openssl req -x509 -new -nodes -sha256 \
        -key certs/ca.key \
        -days 3650 \
        -subj '/O=RabbitMQ Test/CN=Certificate Authority' \
        -out certs/ca.crt

openssl genrsa -out certs/server.key 2048

openssl req -new -sha256 \
        -key certs/server.key \
        -subj '/O=RabbitMQ/CN=Server' | openssl x509 \
                -req -sha256 \
                -CA certs/ca.crt \
                -CAkey certs/ca.key \
                -CAserial certs/ca.txt \
                -CAcreateserial \
                -days 365 \
                -out certs/server.crt
```

Available environment variables:
- `RABBITMQ_SSL_CACERTFILE`
- `RABBITMQ_SSL_CERTFILE`
- `RABBITMQ_SSL_KEYFILE`

### Deployment

Execute the following (and replace parameters as needed):

```sh
docker run -d --rm \
        --name rabbit \
        -v $PWD/certs:/etc/ssl/certs/rabbit \
        -e RABBITMQ_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt \
        -e RABBITMQ_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt \
        -e RABBITMQ_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key \
        -e RABBITMQ_DEFAULT_USER=user \
        -e RABBITMQ_DEFAULT_PASS=p4ss \
        -p 5671:5671 \
        rabbitmq:3.8.14
```

## TLS with management plugin

Available environment variables:
- `RABBITMQ_MANAGEMENT_SSL_CACERTFILE`
- `RABBITMQ_MANAGEMENT_SSL_CERTFILE`
- `RABBITMQ_MANAGEMENT_SSL_KEYFILE`

### Deployment
```sh
docker run -d --rm \
        --name rabbit \
        -v $PWD/certs:/etc/ssl/certs/rabbit \
        -e RABBITMQ_MANAGEMENT_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt \
        -e RABBITMQ_MANAGEMENT_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt \
        -e RABBITMQ_MANAGEMENT_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key__ \
        -e RABBITMQ_DEFAULT_USER=user \
        -e RABBITMQ_DEFAULT_PASS=p4ss \
        -p 15671:15671 \
        rabbitmq:3.8.14-management
```

### Testing

Direct your browser at: https://host:15671, set: user/p4ss.


# TLS with mutual authentication

| Set environment variables | When |
|-|-|
| `RABBITMQ_SSL_VERIFY=true` <br/> `RABBITMQ_SSL_FAIL_IF_NO_PEER_CERT=true` | When running without management GUI |
| `RABBITMQ_MANAGEMENT_SSL_VERIFY=true` <br/> `RABBITMQ_MANAGEMENT_SSL_FAIL_IF_NO_PEER_CERT=true` | When using management GUI |

## Example
```sh
docker run -d --rm --name rabbit \
        -v $PWD/certs:/etc/ssl/certs/rabbit \
        -e RABBITMQ_MANAGEMENT_SSL_VERIFY=verify_peer \
        -e RABBITMQ_MANAGEMENT_SSL_FAIL_IF_NO_PEER_CERT=true \
        -e RABBITMQ_MANAGEMENT_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt \
        -e RABBITMQ_MANAGEMENT_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt \
        -e RABBITMQ_MANAGEMENT_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key \
        -e RABBITMQ_DEFAULT_USER=user \
        -e RABBITMQ_DEFAULT_PASS=p4ss \
        -p 15671:15671 \
        rabbitmq:3.8.14-management
```

# References

[1] https://www.rabbitmq.com/configure.html#configuration-files
