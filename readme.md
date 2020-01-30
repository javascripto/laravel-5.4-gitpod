# Notes

[![Open in Gitpod](https://gitpod.io/button/open-in-gitpod.svg)](https://gitpod.io#https://github.com/javascripto/laravel-5.4-gitpod)
- Create a project with laravel 5.4: `composer create-project --prefer-dist laravel/laravel blog 5.4.*`
- Add some post-install scripts to `composer.json`:

```json
{
    "post-install-cmd": [
        "php -r \"file_exists('.env') || copy('.env.example', '.env');\"",
        "php artisan key:generate"
    ],
}
```
- Add config to `.gitpod/.gitpod.Dockerfile`

```dockerfile

FROM gitpod/workspace-full:latest

USER root

# Install MySQL
RUN apt update \
 && apt install -y mysql-server mysql-client mycli php-sqlite3 php-symfony-debug php-mysql php-zip php-xml \
 && apt clean && rm -rf /var/cache/apt/* /var/lib/apt/lists/* /tmp/* \
 && mkdir /var/run/mysqld \
 && chown -R gitpod:gitpod /etc/mysql /var/run/mysqld /var/log/mysql /var/lib/mysql /var/lib/mysql-files /var/lib/mysql-keyring /var/lib/mysql-upgrade

# Install our own MySQL config
# Install default-login for MySQL clients
COPY .gitpod/mysql.cnf /etc/mysql/mysql.conf.d/mysqld.cnf
COPY .gitpod/client.cnf /etc/mysql/mysql.conf.d/client.cnf
COPY .gitpod/mysql-bashrc-launch.sh /etc/mysql/mysql-bashrc-launch.sh

USER gitpod

RUN echo "/etc/mysql/mysql-bashrc-launch.sh" >> ~/.bashrc
RUN echo "/etc/mysql/mysql-bashrc-launch.sh" >> ~/.zshrc

# More information: https://www.gitpod.io/docs/42_config_docker/
# https://github.com/gitpod-io/workspace-images/blob/master/mysql/Dockerfile
```

- Add gitpod init commands on `.gitpod.yml` ([gitpodify](https://www.gitpod.io/blog/gitpodify)):

```yml
image:
  file: .gitpod/.gitpod.Dockerfile

tasks:
  - init: composer install
    command: php artisan serve

ports:
  - port: 8000
    onOpen: open-preview
  - port: 3306
    onOpen: ignore
```
