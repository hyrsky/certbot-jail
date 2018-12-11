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
    git clone <repo>
    ~/certbot-jail/certonly <email> <domain>
    ```

4. Then create cron job for renewing certificate
    ```sh
    # Attempt to renew daily
    0 3 * * * $HOME/certbot-jail/renew
    ```

## TODO
* Installer script
