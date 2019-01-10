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
terminus site:create $SITE_NAME "$SITE_LABEL" empty
```

The last command may complain that the site name is taken. In that case, try it
with another name instead of the `$SITE_NAME`, and then on success re-export the
`SITE_NAME` var with a new value.

```
composer create-project --remove-vcs shiyan/drupal-pantheon-lando-template:dev-master $SITE_NAME
cd $SITE_NAME

lando init --recipe=pantheon --pantheon-auth=$MACHINE_TOKEN --pantheon-site=$SITE_NAME

git init
git add --all
git commit --message="Started project."
```

Start the project locally.

```
lando start
lando terminus auth:login --machine-token=$MACHINE_TOKEN
lando drush site:install --yes
```

Then deploy it to Pantheon.

```
lando deploy-code
lando push --code=none --database=dev --files=dev
terminus drush $SITE_NAME.dev -- cache:rebuild
```
