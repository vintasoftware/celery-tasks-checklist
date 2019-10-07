## 1. Best Practices
  * [ ] Prefer [RabbitMQ](https://www.rabbitmq.com/) or [Redis](https://redis.io/) as broker (never use a relational database as production broker).
  * [ ] Do not use complex objects in task as parameters. E.g.: Avoid Django model objects:
    ```
    # Good
    @app.task
    def my_task(user_id):
        user = User.objects.get(id=user_id)
        print(user.name)
        # ...
    ```

    ```
    # Bad
    @app.task
    def my_task(user):
        print(user.name)
        # ...
    ```
  * [ ] Do not wait for other tasks inside a task.
  * [ ] Prefer idempotent tasks:
    * "Idempotence is the property of certain operations in mathematics and computer science, that can be applied multiple times without changing the result beyond the initial application." - Wikipedia.
  * [ ] Prefer atomic tasks:
    * "An operation (or set of operations) is atomic ... if it appears to the rest of the system to occur instantaneously. Atomicity is a guarantee of isolation from concurrent processes. Additionally, atomic operations commonly have a succeed-or-fail definition—they either successfully change the state of the system, or have no apparent effect." - Wikipedia.
  * [ ] Retry when possible. But make sure tasks are idempotent and atomic before doing so. [(Retrying)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#retrying)
  * [ ] Set `retry_limit` to avoid broken tasks to keep retrying forever.
  * [ ] Exponentially backoff if things look like they are not going to get fixed soon. Throw in a random factor to avoid cluttering services:
    ```
    def exponential_backoff(task_self):
        minutes = task_self.default_retry_delay / 60
        rand = random.uniform(minutes, minutes * 1.3)
        return int(rand ** task_self.request.retries) * 60

    # in the task
    raise self.retry(exc=e, countdown=exponential_backoff(self))
    ```
  * [ ] Use [`autoretry_for`](http://docs.celeryproject.org/en/master/userguide/tasks.html#automatic-retry-for-known-exceptions) to reduce the boilerplate code for retrying tasks. 
  * [ ] Use [`retry_backoff`](http://docs.celeryproject.org/en/master/userguide/tasks.html#Task.retry_backoff) to reduce the boilerplate code when doing exponential backoff.
  * [ ] For tasks that require high level of reliability, use `acks_late` in combination with `retry`. Again, make sure tasks are idempotent and atomic. [(Should I use retry or acks_late?)](http://docs.celeryproject.org/en/latest/faq.html#faq-acks-late-vs-retry)
  * [ ] Set hard and soft time limits. Recover gracefully if things take longer than expected:
    ```
    from celery.exceptions import SoftTimeLimitExceeded

    @app.task(task_time_limit=60, task_soft_time_limit=45)
    def my_task():
        try:
            something_possibly_long()
        except SoftTimeLimitExceeded:
            recover()
    ```
  * [ ] Use multiple queues to have more control over throughput and make things more scalable. [(Routing Tasks)](http://docs.celeryproject.org/en/latest/userguide/routing.html)
  * [ ] Extend the base task class to define default behaviour. [(Custom Task Classes)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes)
  * [ ] Use canvas features to control task flows and deal with concurrency. [(Canvas: Designing Work-flows)](http://docs.celeryproject.org/en/latest/userguide/canvas.html)

## 2. Monitoring & Tests
  * [ ] Log as much as possible. Use `get_task_logger` to automatically get the task name and unique id as part of the logs.
  * [ ] In case of failure, make sure stack traces get logged and people get notified (services like [Sentry](https://sentry.io) are a good idea).
  * [ ] Monitor activity using Flower. [(Flower: Real-time Celery web-monitor)](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor)
  * [ ] Use `task_always_eager` to test your tasks are geting called.

## 3. Resources to check
  * [ ] [Celery: an overview of the architecture and how it works](https://www.vinta.com.br/blog/2017/celery-overview-archtecture-and-how-it-works/) by [Vinta.](https://www.vinta.com.br/)
  * [ ] [Celery in the wild: tips and tricks to run async tasks in the real world](https://www.vinta.com.br/blog/2018/celery-wild-tips-and-tricks-run-async-tasks-real-world/) by [Vinta.](https://www.vinta.com.br/)
  * [ ] [Celery Best Practices](https://blog.balthazar-rouberol.com/celery-best-practices) by Balthazar Rouberol.
  * [ ] [Dealing with resource-consuming tasks on Celery](https://www.vinta.com.br/blog/2018/dealing-resource-consuming-tasks-celery/) by [Vinta.](https://www.vinta.com.br/)
  * [ ] [Tips and Best Practices](http://celery.readthedocs.io/en/latest/userguide/tasks.html#tips-and-best-practices) from the official documentation. 
  * [ ] [Task Queues](https://www.fullstackpython.com/task-queues.html) by Full Stack Python
[Flower: Real-time Celery web-monitor](http://celery.readthedocs.io/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor) from the official documentation.
  * [ ] [Celery Best Practices: practical approach](https://khashtamov.com/en/celery-best-practices-practical-approach/) by Adil.
  * [ ] [3 GOTCHAS FOR CELERY](https://wiredcraft.com/blog/3-gotchas-for-celery/) from Wiredcraft.
  * [ ] [CELERY - BEST PRACTICES](https://denibertovic.com/posts/celery-best-practices/) by Deni Bertovic.
  * [ ] [Hacker News thread on the above post.](https://news.ycombinator.com/item?id=7909201)
  * [ ] [[video] Painting on a Distributed Canvas: An Advanced Guide to Celery Workflows](https://www.youtube.com/watch?v=XoMu8vhdc-A) by David Gouldin.
  * [ ] [Celery in Production](https://www.caktusgroup.com/blog/2014/09/29/celery-production/) by Dan Poirier from Caktus Group.
  * [ ] [[video] Implementing Celery, Lessons Learned](https://www.youtube.com/watch?v=hmtSe0yPi6I) by Michael Robellard.
  * [ ] [[video] Advanced Celery](https://www.youtube.com/watch?v=gpKMwPoldak&t=1416s) by Ask Solem Hoel.
