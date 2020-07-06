# pappz-laravel-docker
# Simple docker for laravel

- docker-compose exec app bash

- in terminal you can do everything like
 - composer create-project --prefer-dist laravel/laravel .

### Restore mysql from sql file placed in root folder
cat dump-agrohof_sql-202007060414.sql | docker exec -i AGROHOF_database /usr/bin/mysql -u root --password=root_pass database

so cat {use the dumped sql file} | docker exec -i {container name} /usr/bin/mysql -u {root user} --password={root_password} {database name} 