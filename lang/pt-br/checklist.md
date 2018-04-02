## Boas Práticas

- [ ] Prefira [RabbitMQ](https://www.rabbitmq.com/) ou [Redis](https://redis.io/) como broker (nunca use bando de dados relacional como broker de produção)
- [ ] Não use objetos completos em tarefas com parâmetros. Exemplo: Evite objetos Django model
```
# Bom
@app.task
def my_task(user_id):
    user = User.objects.get(id=user_id)
    print(user.name)
    # ...
```

```
# Ruim
@app.task
def my_task(user):
    print(user.name)
    # ...
```
- [ ] Não espere por outras tarefas dentro de uma tarefa.
- [ ] Prefira tarefas indempotentes.
> "Em matemática e ciência da computação, a idempotência é a propriedade que algumas operações têm de poderem ser aplicadas várias vezes sem que o valor do resultado se altere após a aplicação inicial." - Wikipedia
- [ ] Prefira tarefas atômicas.
> "Transação Atômica, em ciência da computação, é uma operação, ou conjunto de operações, em uma base de dados, ou em qualquer outro sistema computacional, que deve ser executada completamente em caso de sucesso, ou ser abortada completamente em caso de erro." - Wikipedia
- [ ] Tente novamente quando possível. Mas tenha certeza que as tarefas são indempotentes e atômicos antes de fazê-lo. [(Retrying)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#retrying)
- [ ] Defina `retry_limit` para evitar tarefas quebradas de fazer tentativas infinitas.
- [ ] Exponenciamente caia fora se coisas parecerem que não vão ser corrigidos logo. Jogue um fator randômico para evitar confusão de serviços.
```
def exponencial_backoff(task_self):
    minutes = task_self.default_retry_delay / 60
    rand = random.uniform(minutes, minutes * 1.3)
    return int(rand ** task_self.request.retries) * 60

# na tarefa
raise self.retry(exc=e, countdown=exponencial_backoff(self))
```
- [ ] Para tarefas que requere alto nível de confiabilidade, use `acks_late` em combinação com `retry`. Novamente, tenha certeza de que são tarefas idempotentes e atômicas. [(Should I use retry or acks_late?)](http://docs.celeryproject.org/en/latest/faq.html#faq-acks-late-vs-retry)
- [ ] Defina tempos de limite duros e suaves. Recupere graciosamente se as coisas demorarem mais tempo que o esperado.
```
from celery.exceptions import SoftTimeLimitExceeded

@app.task(task_time_limit=60, task_soft_time_limit=45)
def my_task():
    try:
        something_possibly_long()
    except SoftTimeLimitExceeded:
        recover()
```
- [ ] Use múltiplas filas para ter mais controle sobre a taxa de transferência e gerar mais escalabilidade. [(Routing Tasks)](http://docs.celeryproject.org/en/latest/userguide/routing.html)
- [ ] Estenda a classe de tarefa base para definir comportamentos padrão. [(Custom Task Classes)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes)
- [ ] Use recursos da tela para controlar fluxos de tarefas e lidar com concorrência. [(Canvas: Designing Work-flows)](http://docs.celeryproject.org/en/latest/userguide/canvas.html)

## Monitoração & Testes

- [ ] Faça logging na medida do possível. Use `get_task_logger` para automaticamente adicionar o nome e o id único da sua task aos logs.
- [ ] No caso de falha, tenha certeza de que os traços de pilha (stack traces) tenham logs e que pessoas sejam notificadas (serviços como [Sentry](https://sentry.io) é uma boa idéia).
- [ ] Monitore atividades usando Flower. [(Flower: Real-time Celery web-monitor)](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor)
- [ ] Use `task_aways_eager` para testar suas tarefas que estão sendo chamadas.

## Recursos

- [Celery: an overview of the architecture and how it works](https://www.vinta.com.br/blog/2017/celery-overview-archtecture-and-how-it-works/) por [Vinta](https://www.vinta.com.br/)
- [Celery in the wild: tips and tricks to run async tasks in the real world](https://www.vinta.com.br/blog/2018/celery-wild-tips-and-tricks-run-async-tasks-real-world/) por [Vinta](https://www.vinta.com.br/)
- [Dealing with resource-consuming tasks on Celery](https://www.vinta.com.br/blog/2018/dealing-resource-consuming-tasks-celery/) por [Vinta](https://www.vinta.com.br/)
- [Dicas e Boas Práticas](http://celery.readthedocs.io/en/latest/userguide/tasks.html#tips-and-best-practices) da documentação oficial.
- [Task Queues](https://www.fullstackpython.com/task-queues.html) por Full Stack Python
- [Flower: Real-time Celery web-monitor](http://celery.readthedocs.io/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor) da documentação oficial

### 2016

- [Celery Best Practices: practical approach](https://khashtamov.com/en/celery-best-practices-practical-approach/) por Adil

### 2015

- [3 GOTCHAS FOR CELERY](https://wiredcraft.com/blog/3-gotchas-for-celery/) do Wiredcraft

### 2014

- [CELERY - BEST PRACTICES](https://denibertovic.com/posts/celery-best-practices/) por Deni Bertovic
- [Hacker News thread on the above post](https://news.ycombinator.com/item?id=7909201)
- [[video] Painting on a Distributed Canvas: An Advanced Guide to Celery Workflows](https://www.youtube.com/watch?v=XoMu8vhdc-A) por David Gouldin
- [Celery in Production](https://www.caktusgroup.com/blog/2014/09/29/celery-production/) por Dan Poirier do Caktus Group
- [[video] Implementing Celery, Lessons Learned](https://www.youtube.com/watch?v=hmtSe0yPi6I) por Michael Robellard

### 2012

- [[video] Advanced Celery](https://www.youtube.com/watch?v=gpKMwPoldak&t=1416s) por Ask Solem Hoel

### Não datado

- [Celery Best Practices](https://blog.balthazar-rouberol.com/celery-best-practices) por Balthazar Rouberol
