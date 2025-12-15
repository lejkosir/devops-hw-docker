FROM debian:bullseye

ENV DEBIAN_FRONTEND=noninteractive
ENV MYSQL_ROOT_PASSWORD=skrito123
ENV MYSQL_DATABASE=taprav-fri
ENV MYSQL_USER=user
ENV MYSQL_PASSWORD=skrito123

RUN apt-get update && \
    apt-get install -y curl git php php-mysql mysql-server && \
    rm -rf /var/lib/apt/lists/*

RUN git clone -b relative https://github.com/MatejBokal/devops-spletna /tmp/devops-spletna

RUN echo "port=3307" >> /etc/mysql/mysql.conf.d/mysqld.cnf && \
    sed -i 's/^bind-address\s*=.*/bind-address = 0.0.0.0/' /etc/mysql/mysql.conf.d/mysqld.cnf

RUN service mysql start && \
    mysql -e "ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '${MYSQL_ROOT_PASSWORD}'; FLUSH PRIVILEGES;" && \
    mysql -uroot -p${MYSQL_ROOT_PASSWORD} -e "CREATE DATABASE \`${MYSQL_DATABASE}\` CHARACTER SET utf8mb4 COLLATE utf8mb4_general_ci;" && \
    mysql -uroot -p${MYSQL_ROOT_PASSWORD} ${MYSQL_DATABASE} < /tmp/devops-spletna/taprav-fri.sql && \
    mysql -uroot -p${MYSQL_ROOT_PASSWORD} -e "CREATE USER '${MYSQL_USER}'@'%' IDENTIFIED BY '${MYSQL_PASSWORD}'; GRANT ALL PRIVILEGES ON \`${MYSQL_DATABASE}\`.* TO '${MYSQL_USER}'@'%'; FLUSH PRIVILEGES;"

EXPOSE 3307

CMD ["mysqld_safe"]
