## Best Practices

- [ ] [RabbitMQ](https://www.rabbitmq.com/)와 [Redis](https://redis.io/)를 브로커(broker)로 쓰세요. (상용 환경에서 RDB를 브로커로 쓰지 마세요.)
- [ ] 복잡한 object를 태스크에 파라미터로 넘기지 마세요. (예를 들면 Django의 model objects를 파라미터러 사용하는 것을 피하세요.

```
# 좋은 예시
@app.task
def my_task(user_id):
    user = User.objects.get(id=user_id)
    print(user.name)
    # ...
```

```
# 나쁜 예시
@app.task
def my_task(user):
    print(user.name)
    # ...
```
- [ ] 태스크 안에서 태스크를 기다리게 하지 마세요.
- [ ] 태스크가 멱등이 되도록 지향하세요.
> "Idempotence is the property of certain operations in mathematics and computer science, that can be applied multiple times without changing the result beyond the initial application." - Wikipedia
- [ ] 태스크가 원자성을 가지도록 지향하세요.
> "An operation (or set of operations) is atomic ... if it appears to the rest of the system to occur instantaneously. Atomicity is a guarantee of isolation from concurrent processes. Additionally, atomic operations commonly have a succeed-or-fail definition—they either successfully change the state of the system, or have no apparent effect." - Wikipedia
- [ ] 가능하면 재시도하세요. 단, 실행전에 멱등성과 원자성을 가지는 것을 확실히 하세요.
[(Retrying)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#retrying)
- [ ] `retry_limit`를 설정해서 문제있는 태스크가 계속 재시도하지 않도록 하세요. 
- [ ] 태스크가 금방 고쳐질 것 같지 않다면 재시도 횟수를 지수적으로 감소시키세요.([exponentially backoff](https://en.wikipedia.org/wiki/Exponential_backoff)) 혼란스러운 서비스를 피하려면 랜덤 요인을 사용하세요.(수정 필요한 문장 HELP!)

```
def exponential_backoff(task_self):
    minutes = task_self.default_retry_delay / 60
    rand = random.uniform(minutes, minutes * 1.3)
    return int(rand ** task_self.request.retries) * 60

# in the task
self.retry(exc=e, countdown=exponential_backoff(self))
```
- [ ] 높은 신뢰도가 필요한 태스크에서는 `acks_late`와 `retry`를 함께 사용하세요. 물론 멱등성과 원자성을 가지도록 해야합니다. [(Should I use retry or acks_late?)](http://docs.celeryproject.org/en/latest/faq.html#faq-acks-late-vs-retry)
- [ ] 최대 제한([hard limit]())과 경고 제한([soft limit]()) 시간을 설정하세요. 태스크가 예상보다 오래 걸린다면 우아하게 복구하세요. 

```
from celery.exceptions import SoftTimeLimitExceeded

@app.task(task_time_limit=60, task_soft_time_limit=45)
def my_task():
    try:
        something_possibly_long()
    except SoftTimeLimitExceeded:
        recover()
```
- [ ] 처리량과 확정성 제어하기 위해서 큐를 여러개 사용하세요. [(Routing Tasks)](http://docs.celeryproject.org/en/latest/userguide/routing.html)
- [ ] 기본적인 작업정의하려면 베이스 태스크 클래스를 상속하세요. [(Custom Task Classes)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes)
- [ ] 태스크 흐름과 동시성을 제어하기 위해서 Canvas 기능을 사용하세요. [(Canvas: Designing Work-flows)](http://docs.celeryproject.org/en/latest/userguide/canvas.html)

## Monitoring & Tests

- [ ] 가능한한 더 많은 로그를 남기세요. 
- [ ] 실패했을때 stack trace를 남기고 사람이 알 수 있도록 하세요. ([Sentry](https://sentry.io) 같은 서비스를 쓰는게 좋아요) 
- [ ] [(Flower: Real-time Celery web-monitor)](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor)를 사용해서 모니터링 하세요. 
- [ ] 당신의 태스크 호출을 테스트할때 `task_always_eager`를 사용하세요.

## Resources

- [Tips and Best Practices](http://celery.readthedocs.io/en/latest/userguide/tasks.html#tips-and-best-practices) from the official documentation.
- [Task Queues](https://www.fullstackpython.com/task-queues.html) by Full Stack Python
- [Flower: Real-time Celery web-monitor](http://celery.readthedocs.io/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor) from the official documentation.

### 2016

- [Celery Best Practices: practical approach](https://khashtamov.com/en/celery-best-practices-practical-approach/) by Adil

### 2015

- [3 GOTCHAS FOR CELERY](https://wiredcraft.com/blog/3-gotchas-for-celery/) from Wiredcraft

### 2014

- [CELERY - BEST PRACTICES](https://denibertovic.com/posts/celery-best-practices/) by Deni Bertovic
- [Hacker News thread on the above post](https://news.ycombinator.com/item?id=7909201)
- [[video] Painting on a Distributed Canvas: An Advanced Guide to Celery Workflows](https://www.youtube.com/watch?v=XoMu8vhdc-A) by David Gouldin
- [Celery in Production](https://www.caktusgroup.com/blog/2014/09/29/celery-production/) by Dan Poirier from Caktus Group
- [[video] Implementing Celery, Lessons Learned](https://www.youtube.com/watch?v=hmtSe0yPi6I) by Michael Robellard

### 2012

- [[video] Advanced Celery](https://www.youtube.com/watch?v=gpKMwPoldak&t=1416s) by Ask Solem Hoel

### Undated

- [Celery Best Practices](https://blog.balthazar-rouberol.com/celery-best-practices) by Balthazar Rouberol
