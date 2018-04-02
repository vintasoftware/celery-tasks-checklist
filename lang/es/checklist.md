## Buenas Prácticas

- [ ] Prefiera [RabbitMQ](https://www.rabbitmq.com/) o [Redis](https://redis.io/) como broker (nunca use una base de datos relacional como broker en producción.
- [ ] No use objetos complejos como parámetros de las tareas. Por ejemplo: evite usar instancias de modelos de Django.

```
# Correcto
@app.task
def my_task(user_id):
    user = User.objects.get(id=user_id)
    print(user.name)
    # ...
```

```
# Incorrecto
@app.task
def my_task(user):
    print(user.name)
    # ...
```
- [ ] No espere por otras tareas dentro de una tarea.
- [ ] Prefiera tareas idempotentes.
> "En matemática y lógica, la idempotencia es la propiedad para realizar una acción determinada varias veces y aun así conseguir el mismo resultado que se obtendría si se realizase una sola vez." - Wikipedia
- [ ] Prefiera tareas atómicas.
> "Transación Atómica, en Ciencias de la Computación, es una operación, o conjunto de operaciones, en una base de datos, o en cualquier otro sistema computacional, que debe ser ejecutada completamente en caso de éxito o ser abortada completamente en caso de error." - Wikipedia
- [ ] Vuelva a procesar mientras sea posible; pero asegurese de que las tareas sea atómicas antes de hacerlo. [(Ver documentación: Retrying)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#retrying)
- [ ] Defina `retry_limit` para evitar que tareas dañadas se mantengan intentando procesar infinitamente.
- [ ] Use retroceso exponencial si las cosas parecen no arreglarse pronto. Use un factor aleatorio para evitar desordenar los servicios.
```
def exponential_backoff(task_self):
    minutes = task_self.default_retry_delay / 60
    rand = random.uniform(minutes, minutes * 1.3)
    return int(rand ** task_self.request.retries) * 60

# in the task
raise self.retry(exc=e, countdown=exponential_backoff(self))
```
- [ ] Para tareas que requieren un alto nivel de confiabilidad, use `acks_late` en combinación con `retry`. De nuevo, asegúrese que las tareas son edempotentes y atómicas. [(Ver documentación: Should I use retry or acks_late?)](http://docs.celeryproject.org/en/latest/faq.html#faq-acks-late-vs-retry)
- [ ] Establezca límites de tiempo duros y suaves. Recupérese elegantemente si las cosas toman más de lo esperado.
```
from celery.exceptions import SoftTimeLimitExceeded

@app.task(task_time_limit=60, task_soft_time_limit=45)
def my_task():
    try:
        something_possibly_long()
    except SoftTimeLimitExceeded:
        recover()
```
- [ ] Use múltiples colas para tener más control sobre el rendimiento y hacer las cosas más escalables. [(Ver documentación: Routing Tasks)](http://docs.celeryproject.org/en/latest/userguide/routing.html)
- [ ] Extienda la clase de tarea básica para definir su propia lógica por defecto.[(Ver documentación: Custom Task Classes)](http://docs.celeryproject.org/en/latest/userguide/tasks.html#custom-task-classes)
- [ ] Use el lienzo para controlar flujos de tareas y manejar la concurrencia. [(Ver documentación: Canvas Designing Work-flows)](http://docs.celeryproject.org/en/latest/userguide/canvas.html)

## Monitoreo y Pruebas

- [ ] Genere registros lo más que pueda. Use `get_task_logger`  para automáticamente obtener el nombre de la tarea y su identificador único como parte del registro. 
- [ ] En caso de fallo, asegúrese que los seguimientos de pila sean registrados y que las personas sean notificadas. (servicios como [Sentry](https://sentry.io) are a good idea).
- [ ] Use Flower [(Flower: Real-time Celery web-monitor)](http://docs.celeryproject.org/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor) para monitorear la actividad de las tareas.
- [ ] Use `task_always_eager` para probar que sus tareas están siendo ejecutadas.

## Recursos

- [Celery: an overview of the architecture and how it works](https://www.vinta.com.br/blog/2017/celery-overview-archtecture-and-how-it-works/) por [Vinta](https://www.vinta.com.br/)
- [Celery in the wild: tips and tricks to run async tasks in the real world](https://www.vinta.com.br/blog/2018/celery-wild-tips-and-tricks-run-async-tasks-real-world/) por [Vinta](https://www.vinta.com.br/)
- [Dealing with resource-consuming tasks on Celery](https://www.vinta.com.br/blog/2018/dealing-resource-consuming-tasks-celery/) por [Vinta](https://www.vinta.com.br/)
- [Trucos y Mejores Prácticas](http://celery.readthedocs.io/en/latest/userguide/tasks.html#tips-and-best-practices) de la documentación oficial.
- [Colas de Tareas](https://www.fullstackpython.com/task-queues.html) por Full Stack Python
- [Flower: Monitor de Celery en tiempo real](http://celery.readthedocs.io/en/latest/userguide/monitoring.html#flower-real-time-celery-web-monitor) de la documentación oficial.

### 2016

- [Celery - Mejores prácticas: enfoque práctico](https://khashtamov.com/en/celery-best-practices-practical-approach/) por Adil

### 2015

- [3 pilladas para Celery](https://wiredcraft.com/blog/3-gotchas-for-celery/) por Wiredcraft

### 2014

- [CELERY - MEJORES PRÁCTICAS](https://denibertovic.com/posts/celery-best-practices/) por Deni Bertovic
- [Hilo en Hacker News sobre el post anterior](https://news.ycombinator.com/item?id=7909201)
- [[video] Pintando en un lienzo distribuido: Una guía avanzada a los flujos de trabajo de Celery](https://www.youtube.com/watch?v=XoMu8vhdc-A) por David Gouldin
- [Celery en Producción](https://www.caktusgroup.com/blog/2014/09/29/celery-production/) por Dan Poirier de Caktus Group
- [[video] Implementando Celery. Lecciones aprendidas](https://www.youtube.com/watch?v=hmtSe0yPi6I) por Michael Robellard

### 2012

- [[video] Celery Avanzado](https://www.youtube.com/watch?v=gpKMwPoldak&t=1416s) por Ask Solem Hoel

### Sin fecha

- [Mejores pácticas para Celery](https://blog.balthazar-rouberol.com/celery-best-practices) por Balthazar Rouberol
