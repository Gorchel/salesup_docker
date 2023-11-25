1) cp .env.example .env
2) Необходимо прописать в .env полный путь к проету PATH_SALESUP=/Users/oleg/Sites/salesup.local/
3) скопировать из examples/php файлы в containers.php
5) docker compose up -d --build
6) docker exec -i salesup_php bash -c "cd salesup && composer install"
7) Сайт по адресу salesup.local
-----

Запрашиваем настройки .env для проекта

-----

Заходим в phpmyadmin и создаем бд "geometry" с типом utf8_general_ci 

Далее проводим миграции

docker exec -i salesup_php bash -c "cd salesup && php artisan migrate"

-----

Заполняем таблица из crm

    docker exec -i salesup_php bash -c "cd salesup && php artisan update_tables:init property true"
 
    docker exec -i salesup_php bash -c "cd salesup && php artisan update_tables:init company true"
 
    docker exec -i salesup_php bash -c "cd salesup && php artisan update_tables:init contact true"
 
    docker exec -i salesup_php bash -c "cd salesup && php artisan update_tables:init orders true"