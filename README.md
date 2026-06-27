# Домашнее задание к занятию "GitLab" - Сазонова Дарья

### Задание 1

**Что нужно сделать:**

1. Разверните GitLab локально, используя Vagrantfile и инструкцию, описанные в [этом репозитории](https://github.com/netology-code/sdvps-materials/tree/main/gitlab).   
2. Создайте новый проект и пустой репозиторий в нём.
3. Зарегистрируйте gitlab-runner для этого проекта и запустите его в режиме Docker. Раннер можно регистрировать и запускать на той же виртуальной машине, на которой запущен GitLab.

В качестве ответа в репозиторий шаблона с решением добавьте скриншоты с настройками раннера в проекте.

### Решение 1

![Решение 1](https://github.com/darjalintu/gitlab_hw/blob/main/img/Задание_1.png)

---

### Задание 2

**Что нужно сделать:**

1. Запушьте [репозиторий](https://github.com/netology-code/sdvps-materials/tree/main/gitlab) на GitLab, изменив origin. Это изучалось на занятии по Git.
2. Создайте .gitlab-ci.yml, описав в нём все необходимые, на ваш взгляд, этапы.

В качестве ответа в шаблон с решением добавьте: 
   
 * файл gitlab-ci.yml для своего проекта или вставьте код в соответствующее поле в шаблоне; 
 * скриншоты с успешно собранными сборками.

### Решение 2

Файл gitlab-ci.yml
```stages:
  - test
  - static-analysis
  - build

variables:
  GITLAB_URL: "http://111.88.153.183/"
  SONAR_PROJECT_KEY: "netology-gitlab"
  DOCKER_DRIVER: overlay2

test:
  stage: test
  image: golang:1.22
  tags:
    - docker
  script:
    - echo "Запускаю Go-тесты..."
    - go version
    - go test -v ./...

sonarqube-check:
  stage: static-analysis
  image:
    name: sonarsource/sonar-scanner-cli:5.0.1
    entrypoint: [""]
  tags:
    - docker
  variables:
    SONAR_HOST_URL: "http://111.88.153.183:9000"
  script:
    - echo "Запускаю анализ в SonarQube..."
    - sonar-scanner -Dsonar.projectKey=$SONAR_PROJECT_KEY -Dsonar.sources=. -Dsonar.host.url=$SONAR_HOST_URL -Dsonar.token=$SONAR_TOKEN
  allow_failure: true

build:
  stage: build
  image: docker:latest
  tags:
    - docker
  services:
    - name: docker:dind
      entrypoint: ["env", "-u", "DOCKER_HOST"]
  variables:
    DOCKER_HOST: tcp://docker:2375
    DOCKER_TLS_CERTDIR: ""
    DOCKER_DRIVER: overlay2
  script:
    - for i in {1..30}; do docker info >/dev/null 2>&1 && break || sleep 2; done
    - docker build -t myapp:$CI_COMMIT_SHORT_SHA .
  when: manual
```
Скриншоты:
![Решение 2](https://github.com/darjalintu/gitlab_hw/blob/main/img/Runner.png)

![Решение 3](https://github.com/darjalintu/gitlab_hw/blob/main/img/Sonarqube.png)

![Решение 3](https://github.com/darjalintu/gitlab_hw/blob/main/img/Pipeline.png)
Не успела победить последний стейдж))
