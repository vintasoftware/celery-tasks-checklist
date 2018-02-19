## Best Practices

- [ ] [RabbitMQ](https://www.rabbitmq.com/) 또는 [Redis](https://redis.io/)를 브로커(broker)로 쓰세요. (상용 환경에서 RDB를 브로커로 쓰지 마세요.)
- [ ] 복잡한 객체(object)를 태스크(task)에 파라미터(parameter)로 사용하지 마세요. (예를 들면 Django의 모델 객체)

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
- [ ] 태스크가 멱등성을 가지도록 하세요.
> "멱등성(冪等性, 영어: idempotence)은 수학이나 전산학에서 연산의 한 성질을 나타내는 것으로, 연산을 여러 번 적용하더라도 결과가 달라지지 않는 성질을 의미한다" - Wikipedia
- [ ] 태스크가 원자성을 가지도록 하세요.
> "어떠한 작업이 실행될때 언제나 완전하게 진행되어 종료되거나, 그럴 수 없는 경우 실행을 하지 않는 경우를 말한다. 원자성을 가지는 작업은 실행되어 진행되다가 종료하지 않고 중간에서 멈추는 경우는 있을 수 없다." - Wikipedia
- [ ] 가능하면 재시도하세요. 단, 실행전에 멱등성과 원자성을 가지는 것을 확실히 하세요.
[(Retrying)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#retrying)
- [ ] `retry_limit`를 설정해서 문제 있는 태스크가 계속 재시도하지 않도록 하세요. 
- [ ] 태스크가 금방 고쳐질 것 같지 않다면 재시도 횟수를 지수적으로 감소시키세요.([exponentially backoff](https://en.wikipedia.org/wiki/Exponential_backoff)) 혼란스러운 서비스를 피하려면 임의의 요소를 사용하세요.

```
def exponential_backoff(task_self):
    minutes = task_self.default_retry_delay / 60
    rand = random.uniform(minutes, minutes * 1.3)
    return int(rand ** task_self.request.retries) * 60

# in the task
raise self.retry(exc=e, countdown=exponential_backoff(self))
```
- [ ] 재시도 태스크에서 boilerplate code를 줄이기 위해서 [`autoretry_for`](http://docs.celeryproject.org/en/master/userguide/tasks.html#automatic-retry-for-known-exceptions) 를 사용하세요.
- [ ] 재시도 횟수를 지수적으로 감소시킬때 boilerplate code를 줄이기 위해서 [`retry_backoff`](http://docs.celeryproject.org/en/master/userguide/tasks.html#Task.retry_backoff) 를 사용하세요.
- [ ] 높은 신뢰도가 필요한 태스크에서는 `acks_late`와 `retry`를 함께 사용하세요. 물론 멱등성과 원자성을 가지도록 해야합니다. [(Should I use retry or acks_late?)](http://docs.celeryproject.org/en/latest/faq.html#faq-acks-late-vs-retry)
- [ ] 최대 제한([hard limit]())과 경고 제한([soft limit]()) 시간을 설정하세요. 태스크가 예상보다 오래 걸린다면 완만하게 복구하세요. 

```
from celery.exceptions import SoftTimeLimitExceeded

@app.task(task_time_limit=60, task_soft_time_limit=45)
def my_task():
    try:
        something_possibly_long()
    except SoftTimeLimitExceeded:
        recover()
```
- [ ] 처리량과 확장성을 제어하기 위해서 큐를 여러개 사용하세요. [(Routing Tasks)](http://docs.celeryproject.org/en/latest/userguide/routing.html)
- [ ] 기본적인 작업을 정의하려면 기본 태스크 클래스를 만들어 상속하세요. [(Custom Task Classes)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes)
- [ ] 태스크 흐름과 동시성을 제어하기 위해서 Canvas 기능을 사용하세요. [(Canvas: Designing Work-flows)](http://docs.celeryproject.org/en/latest/userguide/canvas.html)

## Monitoring & Tests

- [ ] 가능한 한 더 많은 로그를 남기세요. `get_task_logger`를 이용해서 태스크 이름과 고유 id를 자동으로 가져오세요.
- [ ] 실패했을 때 stack trace를 남기고 사람이 알 수 있도록 하세요. ([Sentry](https://sentry.io) 같은 서비스를 쓰는게 좋아요) 
- [ ] [Flower: Real-time Celery web-monitor](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor)를 사용해서 샐러리 모니터링을 하세요. 
- [ ] 태스크 호출을 테스트할 때 `task_always_eager`를 사용하세요.

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
