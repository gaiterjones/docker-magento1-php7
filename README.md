# DOCKER MAGENTO OPEN SOURCE (CE) 1.X CONTAINER

Docker image for Magento 1.9.4 / PHP7

Install Magento 1.9.4.0 for Development and Testing

git clone

# Build

docker-compose build

# Start

docker-compose up -d

# Magento setup and Sample Data

docker exec -it docker-magento1-php7_php-apache_1 install-sampledata

docker exec -it docker-magento1-php7_php-apache_1 install-magento

# Includes n98-magerun and modman

docker exec -it docker-magento1-php7_php-apache_1 n98-magerun.phar config:dump

docker exec -it docker-magento1-php7_php-apache_1 n98-magerun.phar dev:symlinks 1

docker exec -it docker-magento1-php7_php-apache_1 modman init

# Persistant Volumes

Uncomment volume config in docker-compose.yml for persistent volumes for SQL and WWW.

# Connect

http://magento1.dev.com/admin

# MORE

http://blog.gaiterjones.com
