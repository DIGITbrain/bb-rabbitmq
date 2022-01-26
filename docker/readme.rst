========
RabbitMQ
========

About
=====
**RabbitMQ** [1]_ is open source message broker software (sometimes called message-oriented middleware) that implements the Advanced Message Queuing Protocol (AMQP). 

Version
-------
RabbitMQ version **3.9.13-management** deployed based on official Docker Hub image: [2]_.

License
-------
**Mozilla Public License v2.0** [3]_

Pre-requisites
==============
* *docker* installed
* access to DIGITbrain private docker repo (username, password) to pull the image:
  
  - ``docker login dbs-container-repo.emgora.eu``
  - ``docker pull dbs-container-repo.emgora.eu/rabbit:3.9.13``

Usage
=====
.. code-block:: bash

  docker run -d --rm --name rabbit \
    -e RABBITMQ_DEFAULT_USER=user \
    -e RABBITMQ_DEFAULT_PASS=p4ss \
    -p 5671:5671 \
    -p 15671:15671 \
    rabbit:3.9

where RABBITMQ_DEFAULT_USER and RABBITMQ_DEFAULT_PASS parameters create a new database user with the given username and password,
and standard RabbitMQ (TLS) port 5671 is opened on the host with (HTTPS) Management GUI avaialable on port 15671 .

.. warning::
  Always update the ``RABBITMQ_DEFAULT_USER`` and ``RABBITMQ_DEFAULT_PASS`` parameters prior to running this container.

Security
========

The container uses **TLS traffic encryption** and **username-password authentication**. Management GUI is available over HTTPS (same username and password to log in).
You should use the server certificate of your own (see Configuration below).

To check services and open ports run: ``docker exec rabbit rabbitmq-diagnostics listeners``

TLS client authentication is also configurable (via configuration file: ``ssl_options.verify = verify_peer`` and ``ssl_options.fail_if_no_peer_cert = true``, see [4]_).

Configuration
=============

Parameters
----------
.. list-table:: 
   :header-rows: 1

   * - Name
     - Example
     - Comment
   * - *Username*
     - ``-e RABBITMQ_DEFAULT_USER=myuser``
     - Username for a newly created user
   * - *Password*
     - ``-e RABBITMQ_DEFAULT_PASSWORD=mypassword``
     - Password for a newly created user
   * - *CA certificate*
     - ``-e RABBITMQ_SSL_CACERTFILE=/etc/ssl/certs/rabbit/ca.crt``
     - CA certificate location in container
   * - *Server certificate*
     - ``-e RABBITMQ_SSL_CERTFILE=/etc/ssl/certs/rabbit/server.crt``
     - Server certificate location in container
   * - *Server key*
     - ``-e RABBITMQ_SSL_KEYFILE=/etc/ssl/certs/rabbit/server.key``
     - Server key location in container

Ports
-----
.. list-table:: 
  :header-rows: 1

  * - Container port
    - Host port bind example
    - Comment
  * - *RabbitMQ SSL*
    - ``-p 5671:5671``
    - RabbitMQ SSL port 
  * - *RabbitMQ*
    - ``-p 5672:5672``
    - RabbitMQ port (**disabled** by default, ``listeners.tcp = none`` in ``rabbitmq.conf``) 
  * - *RabbitMQ SSL*
    - ``-p 5671:5671``
    - RabbitMQ SSL port 
  * - *HTTPS Management GUI*
    - ``-p 15671:15671``
    - Management GUI port
  * - *HTTP Management GUI*
    - ``-p 15672:15672``
    - Management GUI port (**disabled** by default, commented out ``management.tcp.port = 16672`` in ``rabbitmq.conf``)

Volumes
-------
.. list-table:: 
  :header-rows: 1

  * - Name
    - Volume mount example
    - Comment
  * - *RabbitMQ data*    
    - ``-v $PWD/data:/var/lib/rabbitmq``
    - RabbitMQ data will be persisted in host directory: ``./data``
  * - *RabbitMQ configuration file*    
    - ``-v $PWD/rabbitmq.conf:/etc/rabbitmq/rabbitmq.conf``
    - Custom configuration file for RabbitMQ, see [5]_


  * - *CA certificate*    
    - ``-v $PWD/certificates/ca.crt:/etc/ssl/certs/rabbit/ca.crt``  
    - Certificate Authority certificate
  * - *Server certificate*    
    - ``-v $PWD/certificates/server-cert.pem:/etc/ssl/certs/rabbit/server.crt``  
    - Server certificate in PEM format
  * - *Server key*    
    - ``-v $PWD/certificates/server-key.pem:/etc/ssl/certs/rabbit/server.key``  
    - Server key file in PEM format

References
==========
.. [1] https://www.rabbitmq.com/

.. [2] https://hub.docker.com/_/rabbitmq 

.. [3] https://www.rabbitmq.com/mpl.html

.. [4] https://www.rabbitmq.com/ssl.html

.. [5] https://www.rabbitmq.com/configure.html#configuration-files


