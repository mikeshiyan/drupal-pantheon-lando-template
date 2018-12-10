# Composer template for Drupal projects

Provides a starter kit for:

* Managing your site dependencies with [Composer](https://getcomposer.org/)
* Hosting your site on [Pantheon](https://pantheon.io/)
* Developing locally with [Lando](https://docs.devwithlando.io/)
* Running tests locally.

This template is based on 3 others:

* https://github.com/drupal-composer/drupal-project
* https://github.com/pantheon-systems/example-drops-8-composer
* https://github.com/lando/lando-pantheon-ci-workflow-example

## Prerequisites

* [Install Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git)
* [Install Lando](https://docs.devwithlando.io/installation/installing.html)
* [Install Composer](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos)
* [Install Terminus](https://pantheon.io/docs/terminus/install/)
* [Register at Pantheon](https://pantheon.io/register), add your SSH public key,
and create a machine token.
* Choose a machine-friendly site name. It should be all lower case with dashes
instead of spaces.

## Usage

Run the following commands one at a time.

Set variables for re-use in terminal session. Replace values with yours.

```
export SITE_NAME="my-project"
export SITE_LABEL="My New Shiny Project"
export MACHINE_TOKEN="my_pantheon_machine_token"

terminus auth:login --machine-token=$MACHINE_TOKEN
```

The following command may complain that the site name is taken. In that case,
try it with another name instead of the `$SITE_NAME`, and then on success
re-export the `SITE_NAME` var with a new value.

```
terminus site:create $SITE_NAME "$SITE_LABEL" empty

composer create-project shiyan/drupal-pantheon-lando-template:dev-master $SITE_NAME
cd $SITE_NAME

lando init --recipe=pantheon

git init
git add --all
git commit --message="Started project."
```

Push code and install the site on Pantheon.

```
terminus connection:set $SITE_NAME.dev git
composer prepare-for-pantheon
git add --all
git commit --message="Added everything for Pantheon."
git remote add pantheon $(terminus connection:info $SITE_NAME.dev --field=git_url)
git push --force pantheon master
git reset HEAD^1 && git checkout -- .gitignore
terminus drush $SITE_NAME.dev -- site:install --yes
```

Start the project locally.

```
lando start
lando terminus auth:login --machine-token=$MACHINE_TOKEN
lando pull --code=none --database=dev --files=dev
lando drush cache:rebuild
```
