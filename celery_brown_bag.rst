################
Celery brown bag
################

Work4Labs

---------------
Overview / Goal (laurent)
---------------

Celery is an asynchronous tasks queue. Written in Python, it is composed of a broker that receive tasks orders to be exectudes and
workers that fetch these orders from the broker and execute them.
the main goal of the system is to be asynchronous. That is to say when you ask celery to execute a task, you don't know when it will be really executed.
It can be at once if the task is high priority or a long time later if the system is crowded for instance.

The second characteristic of Celery is to be a distributed system. the central element is the broker that gather tasks orders 
provided by different sources (code, cron, ...). 
Workers though can be on different machines, with different configuration. They just need to have access to the broker to get their tasks to execute.

There is only a python implementation of Celery but, in theory, a worker can be implemented in any language which support AMQP and basic serialization tool (JSON, XML, Pickle ...)

------
Broker
------

Role (laurent)
----

The broker is the queue of the system.
It gather tasks orders store them and distribute them to workers given their priority, arguments ...

Broker implementation can rely on different technologies.
It can be queue managers as RabbitMQ, specialized or rely on databases.

Being developped for queue managment, brokers are reliable and fast brokers. But they must be installed
and configured independently.
Yet use a database usually means you don't have much configuration, integration to do.

broker vs DB

Implementation (shared)
--------------

Difference between main backends:

* RabbitMQ
* Redis
* Mongo


RabbitMQ
~~~~~~~~

* recommanded by Celery project
* AMQP (standard)
* Safe (unelikely to lose data)
* Rock solid (erlang inside)
* Performant and Scalable (examples with 20,000 msg/sec)

Redis
~~~~~

* Fast!
* PUB/SUB support (delay implementation is faster) 
* In memory (risk of data loss)

RabbitMQ and Redis are the only brokers which support all celery features,
and may be required for some specific needs (complex task aknowledgment,
message routing ...).

MongoDB
~~~~~~~

MongoDB beeing already used on work4us, it has been chosen as a broker for work4us
celery implementation.

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

A worker is ... a worker !
It Does the job.

Once executed the worker is splitted in nodes. Each node being an independant process. Each first load the code that may be executed on it
then it fetches relevant messages from the broker and process them.

By default celery start as many nodes as the number of cores in the machine processor. In order to maximise the efficiency.
The amount of nodes can be reduced for instance in case of memory problem to avoid loading the full code each time.

Routing
-------

XXXXXXXXXXXXXXXXXXXXXX

Concurrency (tewfik)
-----------

IO-bound vs CPU-bound

* processes: a worker dispatch the work to several processes => parallelism

* gevent    | event + greenlet (green thread) + monkey patching
* eventlet  | => "fake parallelism" + make third parties libs cooperative => achieve good asynchronicity

* multi-worker: distributed

----------
Celerybeat (laurent)
----------

Role
----

Celerybeat is an additionnal feature provided by celery. It is basically a cron with
advanced logging, routing, and monitoring possibilities.

It just send to the broker tasks order regularly.
Schedules support cronlike description (each day at 1 pm, each month ...)
or frequency description (every  X minutes ...)

Celerybeat can be executed along celery daemon (-B option) or in a separate daemon.
Separate them obviously enable to keep one's execution while the other one is stopped !

------------------
Program for celery (laurent)
------------------

What it looks like?
-------------------

Show CeleryTask::

  from celery import task

  @task
  def export_task(self, job_id, fb_page):
      job = Job.objects.get(id=job_id)
      send_job(job, fb_page)  # sloooowwwwww HTTP request, we have to wait
      job.state = 'active'
      job.save()

  # send a task to the broker
  export_task.delay(42, fb_page)


Celery supports a batch of tools to improve the managment of the tasks processing.
That includes callbacks( what to execute as callback for a specific task), group tasks,
to solve dependancy problems, factorize configuration and timeouts.

Celery uses two timeouts : 

* Soft Timeout : a signal ask the node to raise a Celery SoftTimeOutException.
* Hard timeout : the process is just shut down and the node restarted (kill -9).

By default logging is overwritten by celery which catches all logs from the tasks
and redirect them to the task logger and by default to the celery logger.

What to keep in mind?
---------------------

Best practices
--------------

Think asynchronous 
~~~~~~~~~~~~~~~~~~

* blocking instruction are evil
* Keep your organisation in mind: do not pollute a queue with a task that could prevent others to be executed (dead lock),
* avoid linear coding: don't wait for a task to be executed (unless you have a good reason to do so)

Concurrency create problems
~~~~~~~~~~~~~~~~~~~~~~~~~~~

* think distributed: do not use class variables for anything but reading since objects can be run elswere, twice at the same time ..
* side effects => you will never find your bugs
* since your app is distributed and parallel, you NEED good logging
* locks are not cool

Keep your system healthy
~~~~~~~~~~~~~~~~~~~~~~~~

* monitor your queue length!
* and be ready to add workers when the queue get too long
* remove workers if too many of them are idle
  (remove servers, save power, be ecological, save the world!)

Make your tasks idempotent
~~~~~~~~~~~~~~~~~~~~~~~~~~

Idempotent task: you can run the same task with the sames arguments without
                 destroying the system

acks_late options

Celery will be able to dispatch your tasks more efficiently. Celery workers
reserve several tasks in advance and execute everything in parallel, but if
a worker is overloaded (worse hardware, long task ...) and another is idle,
the 2nd will be able to reserve some tasks of the first one and to execute them.

=> your tasks will be evaluated faster

This principle is especially important if some tasks are way longer than others.

---------
Ecosystem (tewfik)
---------

flower 
~~~~~~

Monitoring interface

screenshot

django-celery
~~~~~~~~~~~~~

Django integration:

* django admin (schedule your task, get the results, errors)
* config

screenshot

monitoring
~~~~~~~~~~

(celerycam) screenshot

---------------
Work4Labs usage
---------------

In work4lasb, we use celery on two parts : In the imports and newly on work4us

Engines (laurent)
-------

On the engines part (works in the whole work4us python code)
adding a celery task is done by creating a class inheriting from CeleryTask or CelerybeatTask (cf lib/python/w4l_celery)

tasks are stored in lib/python/w4l/async_engines/tasks (w4l/task for work4us)

Remark, for now task name must be adde to the __init__.__all__ for it to be registered by celery

the task must have a name matching the module name (and be the snake case of the class name.)
a _process() method (the code executed)

calling the task :
just need to call the apply_async() method of the task instance. ex 
update = UpdateMongoUserCache()
update.apply_async(kwargs={'uid':'1112223334', 'force':false})

then the task is in the broker ready to be executed ... sometimes once the broker is started.
In dev you can start the worker with bin/run_dev if your settings are up to date :)


our crontab is described in : config/crontab/*.beat (* describing the environment : local, demo, prod)


Imports (tewfik)
-------

Import system (Job Pipe) is extensively based on celery, almost all the code
live in celery tasks.

An imports is divided in several steps (crawl, transform, export) and each of
these is splitted in several celery tasks.

Feature used:

* Scheduler: Imports are launched regularly
* Django admin: easy task management
* Parallelism: run a lot of imports, export to several pages...
* Asynchronicity: continue to work when waiting for external services.
* Scalability: prepare the future
