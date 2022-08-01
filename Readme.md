# Magento 2.4.4 for B2B-Store
This project is a dockerized Magento 2.4.4 instance to use with B2B-Store.

## Steps to run
1. Clone the project
2. Start the terminal an go to the _root_ folder of the project (where the `docker-compose.yaml` file is)
3. Start the containers with `docker-compose up -d`
4. If the Opensearch container gives an error during the startup:
    1. Add this line to the bottom of the `/etc/sysctl.conf` (or modify the line in case that already exists): `vm.max_map_count=262144`
    2. Execute `sudo sysctl -p` (or reboot the host system)
5. Enter into the **fpm** container `docker exec -it <fpm_container_name> bash`
6. Adjust the permissions:
   ```
   find var generated vendor pub/static pub/media app/etc -type f -exec chmod g+w {} +
   find var generated vendor pub/static pub/media app/etc -type d -exec chmod g+ws {} +
   chown -R :www-data .
   chmod u+x bin/magento
   ```
7. Run `composer install` to download dependencies.
8. Start the installation (modify the values if needed)
   ```
   bin/magento setup:install \
   --base-url=https://local.magento.com \
   --db-host=db \
   --db-name=magento2 \
   --db-user=magento2 \
   --db-password=magento2 \
   --admin-firstname=admin \
   --admin-lastname=admin \
   --admin-email=admin@admin.com \
   --admin-user=admin \
   --admin-password=admin123 \
   --language=en_US \
   --currency=USD \
   --timezone=America/Chicago \
   --use-rewrites=1 \
   --elasticsearch-host=opensearch-node1 \
   --elasticsearch-port=9200 \
   --elasticsearch-enable-auth=1 \
   --elasticsearch-username=admin \
   --elasticsearch-password=admin \
   --disable-modules='Orienteed_CustomerAgent'
   ```
9. 2FA for admin access can be disabled with:
   ```
   bin/magento module:disable Magento_TwoFactorAuth  
   bin/magento cache:flush
   ```