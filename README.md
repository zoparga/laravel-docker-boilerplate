# Simple docker for laravel

Follow these steps:

- clone this repository
- enter this project directory
- run `cp .env.example .env`
- run `docker-compose up -d`
- wait a few minutes
- enter the app container, run `docker exec -it base_app bash`

// ------ FROM THIS STEP YOU ARE INSIDE THE CONTAINER
- clone the laravel project repository to the current folder
    - e.g.: git clone [REPOSITORY URL] .
    - please pay attention to the '.' mark at the end of the previous line
- follow the basic laravel install steps
    - run `composer install`
    - run `cp .env.example .env`
    - modify `.env` file
        BE CAREFUL, YOU NEED TO CHANGE THE FOLLOWING LINES (PROVIDED THE PROPER VALUES)
        - APP_URL=http://localhost:8089
        - DB_CONNECTION=mysql
        - DB_HOST=database
        - DB_PORT=3306
        - DB_DATABASE=database
        - DB_USERNAME=user
        - DB_PASSWORD=password
    - run `php artisan migrate --seed`
        - Do you wih to continue? (because you are in production) -> Yes
    - run `php artisan key:generate`
    - run `npm install`
    - run `npm run dev`
- open browser, type `http://localhost:8089`
- 🥳✌️



---------------------------------------

# Ports, don't block them!

APP_PHP_PORT=9009
NGINX_80=8089
NGINX_443=4439
MYSQL_PORT=33069

--------------------------------------

# MySQL creditentials

MYSQL_DATABASE=database
MYSQL_USER=user
MYSQL_PASSWORD=password
MYSQL_ROOT_PASSWORD=root_pass

--------------------------------------

### TO RESTORE MYSQL DB IN CONTAINER MYSQL

- place mysql backup (.sql ) file to ./mysql-backup folder
- docker exec -it [APP_NAME]_database bash

When you are in, run the following commands:

- cd /home/mysql-backup
- mysql -u root -p $MYSQL_DATABASE < SQLFILENAME-YOU-COPIED.sql
- enter root password (default is: root_pass)
- 🥳
---------------------------------------


