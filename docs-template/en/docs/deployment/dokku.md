# Deployment to Dokku platform

The following guide assumes that the [official command line client](http://dokku.viewdocs.io/dokku/community/clients/#bash-zsh-etc-dokku_clientsh) `dokku` is installed at the operator's machine.

## Initial setup

In a local operator copy:

```bash
DOKKU_HOST=dokku.me dokku apps:create acme
dokku postgres:create acme
dokku postgres:connect acme < acme.backup.sql
dokku postgres:link acme acme
dokku storage:mount /var/lib/dokku/data/storage/acme/media:/app/var/media
dokku config:set SECRET_KEY=$(pwgen -s 50 -n 1)
dokku config:set \
	ALLOWED_HOSTS=acme.com \
	SENTRY_DSN=https://xxx:yyy@sentry.com/12345
git push dokku master
dokku domains:add acme.com www.acme.com
dokku redirect:set acme www.acme.com acme.com
dokku letsencrypt acme
```

The project is now accessible at <https://acme.com>.

## Update production site

**ATTENTION: The production site is updated automatically! See [Deployment workflow](workflow.md) for details. Please only use this if Gitlab CI is disabled or broken.**

In a local operator copy:

```bash
git push dokku@dokku.me:acme HEAD:master --force
```

This pushes the current local branch to Dokku.