# Docker - Django, Celery & Redis Docker Compose setup 

- DockerFile Config
- Docker compose file config 
- Building a celery application 
- Building a docker image 
- Fixing a problem with celery version 
- Testing a simple celery task 

## Getting started with docker compose 
```markdown
nano docker-compose.yml
```

### Defining Docker services
```yaml
version: "3.8"

services:
  django:
    build: .
    container_name: django
    command: python manage.py runserver 0.0.0.0:8000
    volumes:
      - .:/usr/src/app/
    ports:
      - "8000:8000"
    environment:
      - DEBUG=1
      - DJANGO_ALLOWED_HOSTS=localhost 127.0.0.1
      - CELERY_BROKER=redis://redis:6379/0/
      - CELERY_BACKEND=redis://redis:6479/0
    depends_on:
      - pgdb
      - redis
  celery:
    build: .
    command: celery worker --app=celery_redis_setup --loglevel=info 
    volumes:
      - .:/usr/src/app
    depends_on:
      - django
      - redis
```
Compose file here defines two services: `django` and `celery`
### Django Service 
- Django services uses an image build from the `Dockerfile` in the **current** directory. 
  - Dockerfile pulls a python image from the Docker Hub registry
- Service binds the container and the host machine to the exposed port `8000` (by default, Django port 8000)

```yaml
# Dockerfile 
FROM python:3
ENV PYTHONUNBUFFERED=1
WORKDIR /usr/src/app
COPY requirements.txt ./
RUN pip install -r requirements.txt
```

```shell
# docker-compose.yml
docker compose up
```