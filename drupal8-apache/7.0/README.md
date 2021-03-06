# DRUPAL 8 APACHE/PHP

```Dockerfile
FROM quay.io/continuouspipe/drupal8-apache:7.0
```

## How to build
```bash
docker build --pull --tag quay.io/continuouspipe/drupal8-apache:7.0 --rm .
docker push
```

## How to use

As for all images based on the ubuntu base image, see
[the base image README](../../ubuntu/16.04/README.md)

To finish the installation after basing your image off of this one, assuming you are using docker compose, please run:
```bash
docker-compose up -d database
GITHUB_TOKEN="<your github token>" docker-compose run web /bin/bash /usr/local/share/drupal8/development/install.sh
```

You can also influence the installation process by placing commands in `/usr/local/share/drupal8/install_custom.sh`,
`/usr/local/share/drupal8/install_finalise_custom.sh` or `/usr/local/share/drupal8/development/install_custom.sh`.

These will get run at the end of the respective scripts, `/usr/local/share/drupal8/install.sh` and
`/usr/local/share/drupal8/install_finalise.sh` or `/usr/local/share/drupal8/development/install.sh`.
