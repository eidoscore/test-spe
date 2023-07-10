# test-spe

# Soal Nomor 1

Instalasi Docker pada VM1 & VM2 sudah selesai dilakukan pada masing-masing server

VM1
![Alt text](/image/1.png "a title")

VM2
![Alt text](/image/2.png "a title")


# Soal Nomor 2
disini saya memilih menggunakan Nginx maka saya dan secara default sudah ada di server centos pada VM1

![Alt text](/image/3.png "a title")


# Soal Nomor 3

Buat directory /src dan buat file index.php

> <?php 
> echo "SPE skill Test"
> ?>

kemudian buat 1 Dockerfile

> FROM php:9-apache
> COPY src/index.php /var/www/html/

setelah itu lakukan build docker image dengan perintah

> docker build -t skill-test:latest .


# Soal Nomor 4
untuk port forwarder saya menggunakan socat untuk melakukan port forwarder aplikasi dari VM2 ke VM1, pertama install socat pada VM1

> yum install socat

buat service systemd

>Description=Forwards IPVM2:8080 to VM1:8080
>
>[Service]
>ExecStart=/usr/bin/socat TCP-LISTEN:8080,fork,reuseaddr TCP:IPVM2:8080
>
>[Install]
>WantedBy=multi-user.target


Jalankan docker container menggunakan image sebelumnya

> docker run --name skill-test -p 8080:80 -d skill-test:latest

![Alt text](/image/4.png "a title")

# Soal Nomor 5

Buat docker volume untuk menyimpan semua konfigurasi, template, dll

> docker volume create portainer_data

jalankan container portainer jika ingin menggunakan https & http

> docker run -d -p 8000:8000 -p 9443:9443 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

> docker run -d -p 8000:8000 -p 9000:9000 --name portainer --restart=always -v /var/run/docker.sock:/var/run/docker.sock -v portainer_data:/data portainer/portainer-ce:latest

![Alt text](/image/5.png "a title")

# Soal Nomor 6

Lakukan reverse proxy nonSSL menggunakan nginx pada aplikasi php sebelumnya yang sudah dilakukan port forward

> server {
>   listen 80;
>   server_name skilltest-mon.spesandbox.com;
>
>
>   location / {
>     proxy_pass http://127.0.0.1:8080;
>  }
>
> }


![Alt text](/image/6.png "a title")

# Soal Nomor 7

Lakukan instalasi MySQL 8 pada server VM1 kemudian buat user

> mysql -uroot -p
> CREATE USER "user"@"%" IDENTIFIED WITH mysql_native_password BY "password";

kemudian berikan full akses pada akun

> GRANT ALL ON *.* TO "user"@"%";

> FLUSH PRIVILEGES;


# Soal Nomor 8

Akses user mysql diatas pada VM2

> mysql -u user -h IPVM1 -p

> CREATE DATABASE db_test;
> show databases;


![Alt text](/image/8.png "a title")

# Soal Nomor 9

buat file yaml untuk deployment service dengan NodePort dan Ingress Controller

file yang dibuat ada di directory

/root/test/kubernetes

# Soal Nomor 10
Setup Monitoring server, disini saya menggunakan Grafana, Prometheus & Node Exporter untuk melakukan scraping data pada server, monitoring tools digunakan untuk memonitor resource pada server, berikut dashboard dari Grafana dan configurasi pada node exporter

>
>global:
>  scrape_interval: 5s
>scrape_configs:
>  - job_name: 'prometheus'
>    static_configs:
>      - targets: ['ipserverVM1:9090']
>  - job_name: 'node_exporter'
>    static_configs:
>      - targets: ['ipserverVM1:9100','ipserverVM2:9100']


![Alt text](/image/9.png "a title")
