# Домашнее задание к занятию "`8.2 Что такое DevOps. СI/СD`" - `Барановский Станислав`


### Инструкция по выполнению домашнего задания

   1. Сделайте fork репозитория c шаблоном решения к себе в GitHub и переименуйте его по названию или номеру занятия, например, https://github.com/имя-вашего-репозитория/gitlab-hw или https://github.com/имя-вашего-репозитория/8-03-hw.
   2. Выполните клонирование данного репозитория к себе на ПК с помощью команды `git clone`.
   3. Выполните домашнее задание и заполните у себя локально этот файл README.md:
      - впишите вверху название занятия и вашу фамилию и имя
      - в каждом задании добавьте решение в требуемом виде (текст/код/скриншоты/ссылка)
      - для корректного добавления скриншотов воспользуйтесь [инструкцией "Как вставить скриншот в шаблон с решением](https://github.com/netology-code/sys-pattern-homework/blob/main/screen-instruction.md)
      - при оформлении используйте возможности языка разметки md (коротко об этом можно посмотреть в [инструкции  по MarkDown](https://github.com/netology-code/sys-pattern-homework/blob/main/md-instruction.md))
   4. После завершения работы над домашним заданием сделайте коммит (`git commit -m "comment"`) и отправьте его на Github (`git push origin`);
   5. Для проверки домашнего задания преподавателем в личном кабинете прикрепите и отправьте ссылку на решение в виде md-файла в вашем Github.
   6. Любые вопросы по выполнению заданий спрашивайте в чате учебной группы и/или в разделе “Вопросы по заданию” в личном кабинете.
   
Желаем успехов в выполнении домашнего задания!

---

### Задание 1

1. Установите себе jenkins по инструкции из лекции или любым другим способом из официальной документации. Использовать Docker в этом задании нежелательно
    ```
    sudo apt -y install default-jre
    curl -fsSL https://pkg.jenkins.io/debian/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
    echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
    sudo apt update
    sudo apt -y install jenkins
    http://127.0.0.1:8080
    ```
2. Установите на машину с jenkins [golang](https://golang.org/doc/install).
    ```
    wget https://go.dev/dl/go1.19.4.linux-amd64.tar.gz
    sudo rm -rf /usr/local/go && sudo tar -C /usr/local -xzf go1.19.4.linux-amd64.tar.gz
    export PATH=$PATH:/usr/local/go/bin
    ```
3. Используя свой аккаунт на GitHub, сделайте себе форк [репозитория](https://github.com/netology-code/sdvps-materials.git). В этом же репозитории находится [дополнительный материал для выполнения ДЗ](https://github.com/netology-code/sdvps-materials/blob/main/CICD/8.2-hw.md).
4. Создайте в jenkins Freestyle Project, подключите получившийся репозиторий к нему и произведите запуск тестов и сборку проекта `go test .` и `docker build .`.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

![Конфигурация проекта. Часть 1](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.1.11.jpg "Конфигурация проекта. Часть 1")
![Конфигурация проекта. Часть 2](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.1.12.jpg "Конфигурация проекта. Часть 2")
![Сборка. Часть 1](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.1.2.jpg "Сборка. Часть 1")
![Сборка. Часть 2](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.1.3.jpg "Сборка. Часть 2")

---

### Задание 2

1. Создайте новый проект pipeline.
2. Перепишите сборку из задания 1 на declarative в виде кода.
    ```
    pipeline {
        agent any
        stages {
            stage('Git') {
                steps {git 'https://github.com/StanislavBaranovskii/8-2-hw.git'}
            }
            stage('Build') {
                steps {
                    sh 'go test .'
                    sh 'docker build .'
                }
            }
        }
    }
    ```

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

![Конфигурация проекта](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.2.1.jpg "Конфигурация проекта")
![Сборка 2. Часть 1](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.2.2.jpg "Сборка. Часть 1")
![Сборка 2. Часть 2](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.2.3.jpg "Сборка. Часть 2")

---

### Задание 3

1. Установите на машину Nexus.
```
sudo docker run -d -p 8081:8081 --name nexus sonatype/nexus3
```
2. Создайте raw-hosted репозиторий.
3. Измените pipeline так, чтобы вместо Docker-образа собирался бинарный go-файл. Команду можно скопировать из Dockerfile.
    ```
    pipeline {
        agent any
        stages {
            stage('Git') {
                steps {git 'https://github.com/StanislavBaranovskii/8-2-hw.git'}
            }
            stage('Build') {
                steps {
                    sh 'go build -a -installsuffix nocgo -o app'
                }
            }
        }
    }
    ```
4. Загрузите файл в репозиторий с помощью jenkins.

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

![Конфигурация проекта 3](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.3.1.jpg "Конфигурация проекта")
![Сборка 3](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.3.2.jpg "Сборка")
![Конфигурация проекта Nexus](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.3.3.jpg "Конфигурация проекта Nexus")
![Репозиторий Nexus](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.3.3.jpg "Репозиторий Nexus")

---

### Задание 4

1. Придумайте способ версионировать приложение, чтобы каждый следующий запуск сборки присваивал имени файла новую версию. Таким образом, в репозитории Nexus будет храниться история релизов. Подсказка: используйте переменную BUILD_NUMBER.
    ```
    pipeline {
        agent any
        stages {
            stage('Git') {
                steps {git 'https://github.com/StanislavBaranovskii/8-2-hw.git'}
            }
            stage('Build') {
                steps {
                    sh "go build -a -installsuffix nocgo -o app-${BUILD_NUMBER}"
                    echo "build version \${BUILD_NUMBER} = ${BUILD_NUMBER}"
                }
            }
        }
    }
    ```

В качестве ответа пришлите скриншоты с настройками проекта и результатами выполнения сборки.

![Сборка 4](https://github.com/StanislavBaranovskii/8-2-hw/blob/main/img/8.2.4.jpg "Сборка")

---

### Дополнительные материалы, которые могут быть полезны для выполнения задания

1. [Руководство по оформлению Markdown файлов](https://gist.github.com/Jekins/2bf2d0638163f1294637#Code)

## Дополнительные материалы для выполнения домашних заданий из блока "Введение в DevOps"

- [Дополнительный материал для занятия "8.2. Что такое DevOps. СI/СD"](CICD/8.2-hw.md)

- [Дополнительный материал для занятия "8.3. GitLab"](https://github.com/netology-code/sdvps-materials/tree/main/gitlab)
