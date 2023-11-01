# Лабораторная работа 1.
  ## Цель работы.
  Написать 2 Dockerfile с плохими и хорошими практиками.
  ## Ход работы.
  Для выполнения работы было выбрано простое web-приложение на фреймворке Django.   
  **Dockerfile с плохими практиками:**  
  ```
FROM python:latest AS ugabuga
EXPOSE 8000
WORKDIR /first_django
RUN pip3 install asgiref==3.6.0
RUN pip3 install Django==4.1.7
RUN pip3 install environs==7.3.1
RUN pip3 install sqlparse==0.4.3
RUN pip3 install tzdata==2022.7
COPY . /first_django
ENTRYPOINT ["python3"]
CMD ["manage.py", "runserver", "0.0.0.0:8000"]
  ```
  **Что не так?**  
  1. Использование последней версии вместо какой-то конкретной.
  ```
  FROM python:latest
  ```
  Лучше использовать какую-то конкретную версию python, на которой проект точно работает так, как должен. Хотя у следующих версий python наверняка будет обратная совместимость, лучше не усложнять себе жизнь.  
  2. Использование непонятных имен.  
  ```
  FROM python:latest AS ugabuga
  ```
  Из имен должно быть понятно, что им обозначено. Непонятные имена лучше не использовать.  
  3. Установка каждого пакета отдельной командой.
  ```
  RUN pip3 install asgiref==3.6.0
  RUN pip3 install Django==4.1.7
  RUN pip3 install environs==7.3.1
  RUN pip3 install sqlparse==0.4.3
  RUN pip3 install tzdata==2022.7
  ```
Использование однотипных команд RUN могут сделать Dockerfile менее читабельным из-за большего объема, а также увеличить объем образа.

**Исправленный Dockerfile**
```
FROM python:3 AS builder
EXPOSE 8000
WORKDIR /first_django
COPY requirements.txt /first_django
RUN pip3 install -r requirements.txt --no-cache-dir
COPY . /first_django
ENTRYPOINT ["python3"]
CMD ["manage.py", "runserver", "0.0.0.0:8000"]
```
В исправленном варианте использована конкретная версия python, использованы понятные имена и все необходимые пакеты устанавливаются из файла requirements.txt.  
**Сборка и тестирование**
Сборка осуществлена командой 
```
docker-compose up -d
```
Сборка "плохого" Dockerfile:
![Снимок экрана (2232)](https://github.com/KirillMisilin/Clouds/assets/88585791/7508e45f-5456-4e82-8bee-8ca4c23f5b36)  
Сборка "хорошего" Dockerfile:  
![Снимок экрана (2228)](https://github.com/KirillMisilin/Clouds/assets/88585791/97ccfd95-5386-4f8c-b546-1600f85b500e)  
В обоих случаях контейнер работает корректно:  
![Снимок экрана (2229)](https://github.com/KirillMisilin/Clouds/assets/88585791/eb93bb46-fbcf-4d62-8218-c47981ce690e)  
**Плохие практики для работы с контейнером**  
1. В случае, если контейнер не отвечает, лучше использовать команду ``` docker kill ``` , а не ``` docker stop ```, так как первая команда выполнится гораздо быстрее.
2. Не стоит обновлять файл requirements.txt, прописывая новейшие версии пакетов. Использование других версий пакетов может привести к некорректной работе приложения.
