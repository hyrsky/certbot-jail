# Let's encrypt jail

1. Create aws user with policies
    * Managed policy ``AmazonRoute53ReadOnlyAccess`` 
    * Managed policy ``AmazonRoute53DomainsReadOnlyAccess``
    * Policy from ``./policy.json``


2. Create ``~/.aws/credentials`` with content:
    ```ini
    [default]
    aws_access_key_id=<KEY ID>
    aws_secret_access_key=<KEY SECRET>
    ```

3. Log in to jail and run following commands
    ```sh
    pkg install py36-certbot py36-ansible py36-boto git
    git clone https://github.com/hyrsky/certbot-jail.git
    certbot-jail/certonly <email> <domain>
    ```

4. Then create cron job for renewing certificate
    ```sh
    # Attempt to renew daily
    0 3 * * * $HOME/certbot-jail/renew
    ```

5. Run following commands as root inside FreeNAS
   
   Copy files to boot pool. Certificates are not available if using encrypted pool.
    ```sh
    mkdir -p /certs
    chmod 700 /certs
    ln -s /certs/fullchain.pem /certs/letsencrypt.crt
    ln -s /certs/privkey.pem /certs/letsencrypt.key

    rsync -L /mnt/POOL/iocage/jails/letsencrypt/root/usr/local/etc/letsencrypt/live/DOMAIN/* /certs
    ```

6. Create certificate with identifier letsencrypt
7. Create System > Tunables: SSLDIR (rc.conf) = /certs
8. Create Tasks > Cron Jobs:
    ```sh
    # Attempt to renew daily
    30 3 * * * 
    rsync -L /mnt/POOL/iocage/jails/letsencrypt/root/usr/local/etc/letsencrypt/live/DOMAIN/* /certs && service nginx reload
    ```
9. Enable HTTPS in System > General

## TODO
* Installer script
