################
Celery brown bag
################

Work4Labs

---------------
Overview / Goal (laurent)
---------------

The model

Asynchrone (or not)
Distributed

Use case

------
Broker
------

Role (laurent)
----

What is a broker?

broker vs DB

Implementation (shared)
--------------

Difference between main backends:

* RabbitMQ (AMQP: recommanded)
* Redis
* Mongo

Supported brokers (laurent)
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

Role (laurent)
----

What is a broker?

Concurrency (tewfik)
-----------

IO-bound vs CPU-bound

* process
* gevent
* eventlet

----------
Celerybeat (laurent)
----------

Role
----

Replace a cron.

It sends tasks messages regularly

------------------
Program for celery (laurent)
------------------

What it looks like?

Task, Callback, Group, Delay

Error handling, Timeout, Logging

What to keep in mind?

Best practices

---------
Ecosystem (tewfik)
---------

django-celery (django admin)
flower
monitoring (celerycam)

...

---------------
Work4Labs usage
---------------

Engines (laurent)
-------

Use case, technical spec

Imports (tewfik)
-------

Use case, technical spec
