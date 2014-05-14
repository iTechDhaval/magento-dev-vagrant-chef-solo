# Magento development box with Vagrant and Chef Solo

Development box configured to run Magento CE.

- Ubuntu 12.04 LTS
- All Magento CE [required PHP extensions](http://magento.com/resources/system-requirements) installed:
    - PDO_MySQL
    - simplexml
    - mcrypt
    - hash
    - GD
    - Dom
    - iconv
    - curl
    - SOAP
    - apc
- Other miscellaneous packages installed:
    - git
    - vim
- Apache virtual host + MySQL database setup
    - access via `www.magento.dev` or `magento.dev` on the host machine
    - database details:
        - **database:** magentodb
        - **user:** magento_user
        - **password:** magento_pass
    - database can be accessed from the host machine:
        - **host:** 10.10.10.2
        - use user + password above
    - root user for the database is:
        - **user:** root
        - **password:** root
- Composer installed and available globally
    - Sample `composer.json` includes:
        - QA + static analysis tools (`PHPUnit`, `PHP_CodeSniffer` etc.)
        - miscellaneous magento dev tools (`n98-magerun` etc.)
- XDebug installed and setup to allow remote debugging
    - To remote debug use the following values in your ide / debugger:
        - **host:** 10.10.10.2
        - **remote port:** 9000
        - **ide key:** *leave blank*
- Includes script to install **Magento CE 1.8.1.0** + sample data
    - [n98-magerun](https://github.com/netz98/n98-magerun) used to install magento + sample data
    - installation settings can be changed in `.n98-magerun.yaml`
    - default admin user:
        - **user:** admin
        - **password:** password123

## Prerequisites

- A Ruby environment with the following Gems installed:

    - [Bundler](http://bundler.io/)

- [Virtualbox](https://www.virtualbox.org/)

- [Vagrant](http://www.vagrantup.com/) >=1.5 with the following plugins installed:

    - [vagrant-cachier](https://github.com/fgrehm/vagrant-cachier)
    - [vagrant-ombnibus](https://github.com/schisamo/vagrant-omnibus)
    - [vagrant-librarian-chef](https://github.com/jimmycuadra/vagrant-librarian-chef)
    - [vagrant-vbguest](https://github.com/dotless-de/vagrant-vbguest)
    - [vagrant-hostsupdater](https://github.com/cogitatio/vagrant-hostsupdater)

    ```bash
    vagrant plugin install vagrant-cachier
    vagrant plugin install vagrant-omnibus
    vagrant plugin install vagrant-libraian-chef
    vagrant plugin install vagrant-vbguest
    vagrant plugin install vagrant-hostsupdater
    ```

- [Composer](https://getcomposer.org/)

## Usage

Install required Ruby Gems:

```bash
bundle install
```

Setup and provision the box:

```bash
vagrant up
```

Install composer dependencies (from the VM or host if you have composer installed globally):

```bash
composer install --prefer-dist
```

Install Magento CE 1.8.1.0 + sample data (make sure you modify the installation settings to suit in `.n98-magerun.yaml` before running this)

```bash
vagrant ssh -c "~/install-magento.sh"
```

You should now be able to access the magento store at `http://www.magento.dev`.

## FAQ's

**How can i speed up provisioning?**

At the top of the `Vagrantfile` there is the constant `USE_PRECONFIGURED_LAMP_BOX` which is set to `false` by default. If you set this to `true` a preconfigured LAMP base box will be used instead of a blank ubuntu base box. You should still leave the default recipes in the run list so any config values are set accordingly but any packages that these recipes install will already be installed on the base box speeding up provisioning significantly. You can see more information about the preconfigured base box [here](https://github.com/mike182uk/packer-lamp-vagrant-base-box).

## Notes

I have only tested this on OS X (10.9). In theory it should work on most operating systems, although Windows will probably have a problem using NFS for synced folders.
See NFS alternatives [here](https://docs.vagrantup.com/v2/synced-folders/basic_usage.html).