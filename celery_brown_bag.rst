################
Celery brown bag
################

Work4Labs

---------------
Overview / Goal
---------------

The model

Asynchrone (or not)
Distributed

Use case

------
Broker
------

Role
----

What is a broker?

broker vs DB

Implementation
--------------

Difference between main backends:

* RabbitMQ (AMQP: recommanded)
* Redis
* Mongo

Supported brokers
-----------------

Complete list:

* RabbitMQ
* Redis
* Beanstalk
* MongoDB
* CouchDB
* databases (using SQLAlchemy or the Django ORM)

------
Worker
------

Role
----

What is a broker?

Concurrency
-----------

IO-bound vs CPU-bound

* process
* gevent
* eventlet

----------
Celerybeat
----------

Role
----

Replace a cron.

It sends tasks messages regularly

------------------
Program for celery
------------------

What it looks like?

Task, Callback, Group, Delay

Error handling, Timeout, Logging

What to keep in mind?

Best practices

---------
Ecosystem
---------

django-celery

...

---------------
Work4Labs usage
---------------

Engines
-------

Use case, technical spec

Imports
-------

Use case, technical spec

------
Future
------
