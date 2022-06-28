# Drupal Docksal Boilerplate

**IMPORTANT:** This project is a boilerplate for creating new Drupal projects
using the Docksal local development stack. New projects should be initiated by
first creating a new repository from this template and then completing the set up
steps listed below.

## Boilerplate overview

This boilerplate is based off the [drupal/recommended-project](https://github.com/drupal/recommended-project).

This boilerplate is for Drupal 9 projects (the current Drupal release). When
Drupal 10 is released, the `composer.json` should be reviewed and updated accordingly
to reflect the `drupal/recommended-project` `composer.json` file.

For additional notes on the creation of this boilerplate, refer to the [Savas Drupal Docksal Boilerplate Set Up Notes](https://docs.google.com/document/d/1VDOceZOsgSE-QO5KURkk-RRuDmmW-asnOwlwM_8Hfxw/edit#heading=h.jyz67p6f13gu).

This boilerplate ships with

- A `project-init` command that
  - Generates and commits a `composer.lock` file which is excluded from the boilerplate to prevent Drupal from being pinned to a specific version.
  - Generates and commits a new project specific hash salt
  - Installs a new Drupal site
  - Exports and commits the default configuration for the new Drupal site
  - Exports a project specific database (`docksal/database/seed-database.sql.gz`) for the new Drupal site (with a site UUID that matches the previously exported & committed configuration)
- An `import-db` Docksal command that pulls a database from Google Drive.
  - Alternatively, the `import-db` can be rewritten to pull a database from a hosting environment such as Pantheon.
- MailHog which is configured for capturing outgoing emails.
  - Captured emails can be viewed in MailHog at `http://mail.<VIRTUAL_HOST>`.
  - For more information, see https://docs.docksal.io/service/other/mailhog/.

This boilerplate does not ship with Apache Solr. If Solr is required as a
Search API backend, refer to [the Docksal Apache Solr documentaion](https://docs.docksal.io/service/other/apache-solr/)
or previous projects that relied on Solr.

This boilerplate does not ship with any Drupal contributed modules, however
almost all projects will require some combination of contributed modules. Please
refer to [this list of modules to consider installing](https://docs.google.com/spreadsheets/d/1MoJJUo7fKEXxLy-9TgRn9EVCKu68ujkTZABQyuYsAPI/edit#gid=0)
and consult with a senior Drupal developer to identify contributed modules to
install for your project.

This boilerplate does not ship with a default custom theme. If and when a
custom theme is added to your project, please consider making the following updates
to your project:

- Define a `THEME_NAME` variable in `.docksal/docksal.env`
- Add Docksal commands such as `npm-install`, `build-theme`, `watch-theme` that reference the `THEME_NAME` variable

## New project set up steps

1. Create a new GitHub repository for your new project using this project as a template
1. Clone the new project from the new repository
1. Checkout a new branch locally via `git switch -c project-init`
1. Run `fin project-init`. If successful
    1. Remove the project initialization command and commit that change via `git rm .docksal/commands/project-init && git commit -m 'Remove project-init Docksal command'`
1. Update the `[Project Name]` header below and commit that change.
1. Update the `.docksal/commands/import-db` command. If pulling the database from Google Drive:
    1. Upload `docksal/database/seed-database.sql.gz` to Google Drive, renaming it with a project-specific name.
    1. Make the file publicly accessible (`Anyone on the internet with this link can view`) in Google Drive.
    1. Get the Google Drive file id. This can be obtained by getting the link to the file which includes the file id, for example `https://drive.google.com/file/d/<file-id>/view?usp=sharing`.
    1. Set the `DB_GDRIVE_FILE_ID` variable in `.docksal/commands/import-db` to the Google Drive file id.
    1. Commit the changes to `.docksal/commands/import-db`
    1. Test the database import via `fin import-db`
1. Rebuild the site from scratch via `fin init` and confirm the site installs properly and everything works as expected.
1. Delete this `Drupal Docksal Boilerplate` section from the README and commit that change.
1. Merge all of these changes into the `main` branch of your repository via a pull request.

# [Project Name]

## Getting started

### Requirements

1. [Docksal](https://docksal.io/)
* Install Docksal: ([Linux](https://docs.docksal.io/getting-started/setup/#install-linux)) ([Mac](https://docs.docksal.io/getting-started/setup/#install-macos-docker-for-mac))
  * **IMPORTANT:** Docksal may not work with the most recent version(s) of Docker Desktop. Review the Docksal installation instructions and downgrade Docker Desktop if necessary.
2. Install the [Docksal mkcert addon](https://docs.docksal.io/tools/mkcert/) globally via `fin addon install mkcert -g` to enable https locally.
3. Review this README and follow instructions for local development setup.

### Site installation

* Clone this project
* Change directories into this project
* `fin init` - build your local development environment

### Troubleshooting

If you had any issues with the site installation, see the common errors below:

> docker: Error response from daemon: driver failed programming external connectivity on endpoint docksal-vhost-proxy (8fbf838bfaa846c5bc0907ca7835faf2ad5453505988f259ae79cb8551afbc89): Error starting userland proxy: listen tcp 192.168.64.100:443: bind: cannot assign requested address.
ERROR:  Failed starting the proxy service.

Docksal does not currently work with your version of Docker Desktop. Review the Docksal installation instructions and downgrade Docker Desktop if necessary.

> ERROR: for cli  Cannot start service cli: OCI runtime create failed: container_linux.go:346: starting container process caused "process_linux.go:449: container init caused \"rootfs_linux.go:58: mounting \\\"/var/lib/docker/volumes/dori_project_root/_data\\\" to rootfs \\\"/var/lib/docker/overlay2/c611dc00713e30ede5999960959d21583ecc7f3a7736e29a12ce30922e753a76/merged\\\" at \\\"/var/www\\\" caused \\\"stat /var/lib/docker/volumes/dori_project_root/_data: stale NFS file handle\\\"\"": unknown
ERROR: Encountered errors while bringing up the project.

Go to `System Preferences > Security & Privacy > Privacy > Full Disk Access`, click on the plus sign and press `Command-Shift-G` to search for and then add the `/sbin/nfsd` folder.

## Local development

### Local web address

Run `fin echo-localhost` to view the local web address for this project.

### Capturing email

MailHog is configured for capturing outgoing emails. Captured emails can be
viewed in MailHog at `http://mail.<VIRTUAL_HOST>`.

### Docksal commands

Initialize or reset your environment:
* `fin init` to initially create the local environment and pull a seed database and assets

Standard commands:
* `fin start` or `fin up` - to bring the environment up
* `fin stop` - to bring the environment down
* `fin system stop` - to stop and shutdown Docksal

Custom site-specific commands:
* `fin uli` - generate a one-time login link for the site.
* `fin prep-site` - apply the database updates, import configuration, sanitize the database, and build the theme

### Run Drush commands

`fin drush <command>`

See a list of some common Drush commands below:

* `fin drush cr` - clear cache
* `fin drush cim` - import configuration
* `fin drush cex` - export configuration
* `fin drush updb` - run database updates
* `fin drush uli` - generate a one-time login link for the admin user

### Run Composer commands

Run composer within the container `fin composer <command>`

Examples below:

* `fin composer install`
* `fin composer update`

### Adding new contributed modules

Run `fin composer require drupal/<module_name> -n --prefer-dist -v`
to add it to the list of requirements in composer.json. Then, use drush to
enable the module by running `fin drush en <module_name>`. Be sure to export
the site configuration and commit that as well.

### Patching contributed module

If you need to apply patches (depending on the project being modified, a pull
request is often a better solution), you can do so with the
[composer-patches](https://github.com/cweagans/composer-patches) plugin.

To add a patch to drupal module foobar add to the patches section in `patches/composer.patches.json`:
```json
{
    "patches": {
        "drupal/foobar": {
            "Patch description": "URL or local path to patch"
        }
    }
}
```

### Access to MySQL

The Docksal environment is configured to expose MySQL on port 33306 of the host machine. It can be accessed with the following command:

* `fin db cli`

## Enabling XDebug

Follow the [Docksal instructions for enabling Xdebug](https://docs.docksal.io/tools/xdebug/)

### Performance tuning

If the site is running slowly for you locally (i.e pages take more than a few seconds to load on average),
you may be able to improve performance by allocating additional OS resources to Docker. This is particularly relevant on Mac OS.
Specifically, we recommend the suggestions in Step 1 [of this article](https://markshust.com/2018/01/30/performance-tuning-docker-mac/):

> Once you have (at the very least) a quad-core MacBook Pro with 16GB RAM and an SSD, go to Docker > Preferences > Advanced. Set the “computing resources dedicated to Docker” to at least 4 CPUs and 8.0 GB RAM.

Alternatively, set your RAM usage to half of what your computer has available.

Also note that disabling caching or enabling Xdebug locally will both decrease performance.
