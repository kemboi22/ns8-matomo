# ns8-matomo

This is a  module for [ns8-matomo8] for the Project (https://github.com/matomo-org/matomo).
This is a  module is based on the matomo Docker [matomo.org]  (https://github.com/matomo-org/docker).

Start and configure a Matomo instance:

with PHP FPM + nginx as a proxy
redis caching
MariaDB database The module uses Official matomo image.

1. Rename some references inside the repo:
   ```
   modulename=$(basename $(pwd) | sed 's/^ns8-//')
   git mv imageroot/systemd/user/matomo.service imageroot/systemd/user/${modulename}.service
   git mv tests/matomo.robot tests/${modulename}.robot
   sed -i "s/matomo/${modulename}/g" $(find .github/ * -type f)
   git commit -a -m "Repository initialization"
   ```



1. Adjust `.github/workflows` to your needs. `clean-registry.yml` might
   need the proper list of image names to work correctly. Unused workflows
   can be disabled from the GitHub Actions interface.

1. Commit and push your local changes

## Install

Instantiate the module with:

    add-module ghcr.io/compgeniuses/matomo:latest 1

The output of the command will return the instance name.
Output example:

    {"module_id": "matomo1", "image_name": "matomo", "image_url": "ghcr.io/compgeniuses/matomo:latest"}

## Configure

Let's assume that the dokuwiki istance is named `matomo1`.

Then launch `configure-module`, by setting the following parameters:
## MAriDB Service Configurations

- `MYSQL_ROOT_PASSWORD`: 
- `MARIADB_AUTO_UPGRADE`: 1
- `MARIADB_DISABLE_UPGRADE_BACKUP`: 1

## DB Configurations

- `MYSQL_PASSWORD`: administrator mail address
- `MYSQL_DATABASE`: administrator mail address
- `MYSQL_USER`: administrator mail address
- `MATOMO_DATABASE_ADAPTER`: mysql
- `MATOMO_DATABASE_TABLES_PREFIX`: administrator mail address
- `MATOMO_DATABASE_USERNAME`: administrator mail address
- `MATOMO_DATABASE_PASSWORD`: administrator mail address
- `MATOMO_DATABASE_DBNAME`: administrator mail address
- `MARIADB_AUTO_UPGRADE`: 1
- `MARIADB_INITDB_SKIP_TZINFO`: 1

## matomo env Configurations
- `MATOMO_DATABASE_HOST`: 1
- `PHP_MEMORY_LIMIT`: 2048
- `host`: a fully qualified domain name for the wiki
- `http2https`: enable or disable HTTP to HTTPS redirection
- `lets_encrypt`: enable or disable Let's Encrypt certificate

Example:

    api-cli run module/matomo1/configure-module --data '{}'

The above command will:
- start and configure the matomo instance
- (describe configuration process)
- ...

Send a test HTTP request to the matomo backend service:

    curl http://127.0.0.1/matomo/

## Smarthost setting discovery

Some configuration settings, like the smarthost setup, are not part of the
`configure-module` action input: they are discovered by looking at some
Redis keys.  To ensure the module is always up-to-date with the
centralized [smarthost
setup](https://compgeniuses.github.io/ns8-core/core/smarthost/) every time
matomo starts, the command `bin/discover-smarthost` runs and refreshes
the `state/smarthost.env` file with fresh values from Redis.

Furthermore if smarthost setup is changed when matomo is already
running, the event handler `events/smarthost-changed/10reload_services`
restarts the main module service.

See also the `systemd/user/matomo.service` file.

This setting discovery is just an example to understand how the module is
expected to work: it can be rewritten or discarded completely.

## Uninstall

To uninstall the instance:

    remove-module --no-preserve matomo1

## Testing

Test the module using the `test-module.sh` script:


    ./test-module.sh <NODE_ADDR> ghcr.io/compgeniuses/matomo:latest

The tests are made using [Robot Framework](https://robotframework.org/)

## UI translation

Translated with [Weblate](https://hosted.weblate.org/projects/ns8/).

To setup the translation process:

- add [GitHub Weblate app](https://docs.weblate.org/en/latest/admin/continuous.html#github-setup) to your repository
- add your repository to [hosted.weblate.org]((https://hosted.weblate.org) or ask a compgeniuses developer to add it to ns8 Weblate project
