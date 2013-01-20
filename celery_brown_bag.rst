################
Celery brown bag
################

Work4Labs

---------------
Overview / Goal (laurent)
---------------

Celery is a asynchronous tasks queue. it is composed of a broker that receive tasks orders to be exectudes and
workers that fetch these orders from the broker and execute them.
the main goal of the system is to be asynchronous. That is to say when you ask celery to execute a task, you don't know when it will be really executed.
It can be at once if the task is high priority or a long time later if the system is crowded for instance.

The second characteristic of celery is to be a distributed system. the central element is the broker that gather tasks orders 
provided by different sources (code, cron, ...). 
Workers though can be on different machines, with different configuration. They just need to have access to the broker to get their tasks to execute.

------
Broker
------

Role (laurent)
----
The broker is the queue of the system.
It gather tasks orders store them and distribute them to workers given their priority, arguments ...

Broker implementation can rely on different technologies.
It can be queue managers as RabbitMQ, specialized or rely on databases.

beeing developped for queue managment, broker are reliable and fast brokers. but they must be installed and configured independently.
Yet use a database usually means you don't have much configuration, integration to do.

broker vs DB

Implementation (shared)
--------------

Difference between main backends:

* RabbitMQ (AMQP: recommanded)
* Redis
* Mongo
MongoDB beeing already used on work4us, it has been chosen as a broker for work4us celery implementation.

In spite of beeing a little slower than RabbitMQ for instance,
it was much easier to configure. We basically just had to specify the database to use.

Supported brokers (laurent)
-----------------

celery has been integrated with a lot of possible brokers. Here is a list of the brokers available.

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
