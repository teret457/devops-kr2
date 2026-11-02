# Развёртывание варианта 2

Терешин Артем Дмитриевич, ЭФБО-14-24, номер 22. Ресурс https://notes.devops.local, порт 8002.

Исходный шаблон: https://gitverse.ru/dgimatdinov/kr2-template. Логика app.py и зависимости сохранены; добавлен только комментарий с фамилией.

Доставка выполняется rsync с предварительным --dry-run и исключениями --exclude=.env --exclude=.git. На сервере в ~/kr2 создаётся .env с REDIS_PASSWORD, сгенерированным openssl rand -hex 16, с правами 600. Фактический пароль в репозиторий не включается.

Запуск: docker compose up -d --build --wait. Проверка: docker compose ps, docker compose top, docker compose port app 8002, curl http://127.0.0.1:8002/health. Redis не публикуется. down сохраняет том, down -v для этого проекта не применяется.

Файл nginx/kr2.conf устанавливается в /etc/nginx/sites-available/kr2 и активируется ссылкой. На сервере отдельно создаётся сертификат с SAN notes.devops.local; закрытый ключ имеет права 600 и не копируется на хост. Перед применением выполняются nginx -t и systemctl reload nginx. На хосте используется curl --cacert ~/kr2.crt.

Подтверждены сохранение счётчика после down/up, отсутствие .env в образе, непривилегированные процессы, неизменность UFW и 20 успешных проверок итогового аудита. Pull Request не сливается до защиты.
