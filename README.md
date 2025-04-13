# Numele lucrării de laborator
Crearea unei aplicații multi-container.

# Scopul lucrării
Famialiarizarea cu gestiunea aplicației multi-container creat cu docker-compose.

# Sarcina
Creați o aplicație php pe baza a trei containere: nginx, php-fpm, mariadb, folosind docker-compose.

# Efectuarea

## Directorul containers07
- Sa creat directorul containers07 si in el sa creat directorul mounts/site. In acest director am copiat un fisier php de la perechile de php.

## Directorul .gitignore
- Am creat fisierul .gitignore in directorul containers07 cu continutul:
```
# Ignore files and directories
mounts/site/*
```

## In directorul containers07 am creat directorul nginx cu fisierul default.conf cu continutul
```
server {
    listen 80;
    server_name _;
    root /var/www/html;
    index index.php;
    location / {
        try_files $uri $uri/ /index.php?$args;
    }
    location ~ \.php$ {
        fastcgi_pass backend:9000;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```

## In directorul containers07 sa creat fisierul docker-compose.yml cu continutul:
```
services:
  frontend:
    image: nginx:1.19
    volumes:
      - ./mounts/site:/var/www/html
      - ./nginx/default.conf:/etc/nginx/conf.d/default.conf
    ports:
      - "80:80"
    networks:
      - internal
  backend:
    image: php:7.4-fpm
    volumes:
      - ./mounts/site:/var/www/html
    networks:
      - internal
    env_file:
      - mysql.env
  database:
    image: mysql:8.0
    env_file:
      - mysql.env
    networks:
      - internal
    volumes:
      - db_data:/var/lib/mysql

networks:
  internal: {}

volumes:
  db_data: {}
```

## Am creat fisierul mysql.env cu continutul:
```
MYSQL_ROOT_PASSWORD=secret
MYSQL_DATABASE=app
MYSQL_USER=user
MYSQL_PASSWORD=secret
```

## Am pornit containerele cu comanda:
```
docker-compose up -d
```
![alt imgg](./image/Screenshot%202025-04-13%20150227.png)

## Pentru verificare am deschis adresa: http://localhost
![alt img](./image/Screenshot%202025-04-13%20150352.png)

# Concluzie
-În această lucrare am realizat o aplicație formată din mai multe containere, folosind Docker Compose. Am configurat serviciile Nginx, PHP-FPM și MySQL astfel încât să comunice între ele într-o rețea internă. Lucrarea a demonstrat avantajele virtualizării și gestionării aplicațiilor în medii izolate, simplificând procesul de dezvoltare și testare.

# Intrebari:
## În ce ordine sunt pornite containerele?
- Toate containerele definite sunt pornite simultan, dar rețelele și volumele sunt create înainte de containere.

## Unde sunt stocate datele bazei de date?
- In volumul db_data, care păstrează datele persistente chiar dacă containerul database este oprit sau șters.

## Cum se numesc containerele proiectului?
- Containerele sunt numite corespunzator numelui proiectului unde sunt create + numele la serviciile setate in fisierul docker-compose.yml + _1 , adica containers07-frontend, containers07-backend si containers07-database.

## Trebuie să adăugați încă un fișier app.env cu variabila de mediu APP_VERSION pentru serviciile backend și frontend. Cum se face acest lucru?
- Mai intii cream fisierul app.nev in directorul containers07 cu continutul:
```
APP_VERSION=1.0.0
```
![alt img](./image/Screenshot%202025-04-13%20155324.png)

- In urmatorul pas modificam fisierul docker-compose.yml astfel:
1. in serviciul frontend se adauga env_file cu - app.env
![alt img](./image/Screenshot%202025-04-13%20155719.png)
2. in serviciul backend in env_file se adauga - app.env
![alt img](./image/Screenshot%202025-04-13%20155728.png)