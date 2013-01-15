################
Celery brown bag
################

Work4Labs

---------------
Overview / Goal
---------------

Distributed
Asynchrone (or not)


------
Broker
------

Role
----

RabbitMQ (recommanded)
Redis
Mongo

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

Concurrency
-----------

IO-bound vs CPU-bound

* process
* gevent
* eventlet
