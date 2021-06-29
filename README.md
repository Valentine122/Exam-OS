#Exam-OS
Підключення лише за допомогою ключа SSH

`ssh-keygen` -- Створення ключів як для сервера, так і для клієнта.

`ssh-copy-id server@192.168.32.129` - Скопіюйте ключові файли з клієнта на сервер, знаючи пароль сервера (приклад).

`ssh server@192.168.32.129` - Підключення лише за допомогою клавіш (приклад).

### Створення даемона, який запускає сценарій оболонки за допомогою таймера

Наприклад, щоб запускати сценарій кожні 10 секунд, нам потрібно створити службу та таймер для нього.

__/etc/systemd/system/logger-exam.service__:
```Shell
[Unit]
Description=Logger
Wants=logger-exam.timer

[Service]
Type=oneshot
ExecStart=/bin/bash /home/server/exam/shell-script.sh

[Install]
WantedBy=multi-user.target
```

__/etc/systemd/system/logger-exam.timer__:
```Shell
[Unit]
Requires=logger-exam.service

[Timer]
Unit=logger-exam.service
OnCalendar=*-*-* *:*:0/10

[Install]
WantedBy=timers.target
```

__shell-script.sh__:
```Shell
#!/bin/bash

echo "Here I am" >> /home/server/logs
```

Щоб увімкнути виконання системного демона, нам потрібно виконати: `systemctl start logger-exam.timer` (у цьому прикладі).

### Модифікація в програмі Java для включення кінцевої точки Healthcheck

Нам потрібно додати окремий клас контролера для представлення нашої кінцевої точки / healthcheck :

```Java
package com.pazyniuk.controller;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

@RestController
public class HealthController {
  
  @RequestMapping(value = "/healthcheck", method = RequestMethod.GET)
  public ResponseEntity returnHealthCheckResponse() {
      return new ResponseEntity<>("Everything is OK", HttpStatus.OK);
  }
}
```

### Включіть опцію перевірки працездатності для складання докера

Нам потрібно додати позицію перевірки стану здоров’я до нашого файлу docker-compose.yml:

healthcheck: 
```Dockerfile
healthcheck:
      test: ["CMD", "curl", "-f", "http://localhost:8080/healthcheck"]
      interval: 30s
      timeout: 15s
      retries: 5
      start_period: 60s
```

Пізніше ми можемо запустити `docker inspect <container-id>` перевірку працездатності нашого контейнера.

### Докер-контейнер

Щоб запустити контейнер: `docker-compose build --no-cache` and `docker-compose up`.

Для підключення до оболонки контейнера: `docker exec -it <container-id> /bin/bash`.

Для того, щоб скопіювати файл з контейнера хоста: `docker cp <containerId>:/file/path/within/container /host/path/target`.
