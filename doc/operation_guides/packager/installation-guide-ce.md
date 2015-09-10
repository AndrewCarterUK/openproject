*Please be aware that Web Server and Website Management Tools like Plesk
and others do not work well together with our packaged solution. We
strongly recommend to install OpenProject CE on a clean install.*

# OpenProject Community Edition (CE) installation via package manager

The installation of the OpenProject software can be done manually or via official software-packages build by the packager.io service. Using these software packages is highly recommended to reduce the pain of installation and configuration errors. Additionally, the installation via package manager is done via configuration wizard which is very helpful to get everything up and running right from the beginning.

## Stack used by packager.io packages

* Unicorn (application server) – this component hosts the actual application. Depending on the settings chosen, there are at least two unicorn processes running in parallel on the app server machine.
* Apache 2 (web server) – this component provides the external interface, handles SSL termination (if SSL is used) and distributes/forwards web requests to the unicorn processes.
* MySQL (database management system) – this component is used to store and retrieve data.
Ruby 2.1.3 (MRI) and necessary libraries to run the OpenProject source code.

# Run Installation

The installation procedure assumes the following prerequisites to be met:

* A server running one of the following Linux distributions:

| Distribution (64 bits only)     | Identifier   | init system |
| :------------------------------ | :----------- | :---------- |
| Ubuntu 14.04 Trusty             | ubuntu-14.04 | upstart     |
| Debian 8 Jessie                 | debian-8     | systemd     |
| Debian 7 Wheezy                 | debiani-7    | sysvinit    |
| CentOS/RHEL 7.x                 | centos-7     | systemd     |
| CentOS/RHEL 6.x                 | centos-6     | upstart     |
| Fedora 20                       | fedora-20    | sysvinit    |
| Suse Linux Enterprise Server 12 | sles-12      | sysvinit    |

* A mail server that is accessible via SMTP that can be used for sending notification emails. OpenProject supports authentication, yet does not provide support for SMTP via SSL/TLS.

If you intend to use SSL for OpenProject CE:

* A valid SSL certifificate along with the private key file. The files must not be protected by a pass phrase.
* Note that as the installation procedure will automatically create a separate user to run OpenProject CE as, it is not necessary to create such a user beforehand.

The following steps have to be performed to initiate the actual installation of OpenProject CE via package manager. Note that all commands should either be run as root or should be prepended by sudo otherwise (typically depending what linux distribution is used).

## Debian 7 Wheezy

    # install https support
    apt-get install apt-transport-https

    sudo wget -qO - https://deb.packager.io/key | apt-key add -
    echo "deb https://deb.packager.io/gh/finnlabs/pkgr-openproject-community wheezy stable/4.2" | sudo tee /etc/apt/sources.list.d/pkgr-openproject-community.list
    sudo apt-get update
    sudo apt-get install openproject-ce

## Debian 8 Jessie

    # install https support
    apt-get install apt-transport-https

    wget -qO - https://deb.packager.io/key | sudo apt-key add -
    echo "deb https://deb.packager.io/gh/finnlabs/pkgr-openproject-community jessie stable/4.2" | sudo tee /etc/apt/sources.list.d/pkgr-openproject-community.list
    sudo apt-get update
    sudo apt-get install openproject-ce

## Ubuntu 14.04 Trusty

    wget -qO - https://deb.packager.io/key | sudo apt-key add -
    echo "deb https://deb.packager.io/gh/finnlabs/pkgr-openproject-community trusty stable/4.2" | sudo tee /etc/apt/sources.list.d/pkgr-openproject-community.list
    sudo apt-get update
    sudo apt-get install openproject-ce

## Fedora 20

    sudo rpm --import https://rpm.packager.io/key
    echo "[openproject]
    name=Repository for opf/openproject application.
    baseurl=https://rpm.packager.io/gh/finnlabs/pkgr-openproject-community/fedora20/stable/4.2
    enabled=1" | sudo tee /etc/yum.repos.d/pkgr-openproject-community.repo
    sudo yum install openproject-ce

## CentOS / RHEL 6.x

    sudo rpm --import https://rpm.packager.io/key
    echo "[openproject]
    name=Repository for opf/openproject application.
    baseurl=https://rpm.packager.io/gh/finnlabs/pkgr-openproject-community/centos6/stable/4.2
    enabled=1" | sudo tee /etc/yum.repos.d/pkgr-openproject-community.repo
    sudo yum install openproject-ce

## CentOS / RHEL 7.x

    sudo rpm --import https://rpm.packager.io/key
    echo "[openproject]
    name=Repository for opf/openproject application.
    baseurl=https://rpm.packager.io/gh/finnlabs/pkgr-openproject-community/centos7/stable/4.2
    enabled=1" | sudo tee /etc/yum.repos.d/pkgr-openproject-community.repo
    sudo yum install openproject-ce

## Suse Linux Enterprise Server 12

    sudo rpm --import https://rpm.packager.io/key
    sudo zypper addrepo "https://rpm.packager.io/gh/finnlabs/pkgr-openproject-community/sles12/stable/4.2" "pkgr-openproject-community"
    sudo zypper install openproject-ce

# Post-Install Configuration

After the installation of the OpenProject package the system has to be configured to use this package and operate the OpenProject application. Therefore the package includes a configuration wizard which can be started using the following command.

    openproject-ce configure

Side note: The installer supports the configuration of necessary SSL connections too. If required the corresponding SSL certificates (incl. keys) have to be placed on the machine.

# OpenProject command line tool

The openproject package comes with a command line tool to help manage important configuration settings. To see all possible command options of this tool type `sudo openproject-ce`.

    admin@openproject-demo:~# sudo openproject-ce
    Usage:
      openproject-ce run COMMAND [options]
      openproject-ce scale TYPE=NUM
      openproject-ce logs [--tail|-n NUMBER]
      openproject-ce config:get VAR
      openproject-ce config:set VAR=VALUE
      openproject-ce reconfigure

## Set configuration options

During the installation process a lot of settings were set to get the application running. But if you need to set some advanced options or you want to change some settings which were set during the installation via the installation wizard you can use `config:set <option>`. Please be aware that you have to stop and restart the application server so that the new configuration is loaded. This can be done using the `service openproject [start|stop]` command (this is the command for Ubuntu, the command for your distrobution may vary). See which settings can be set and an example for setting the session store below.

    DATABASE_URL=mysql2://openproject:9ScapYA1MN7JQrPR7Wkmp7y99K6mRHGU@127.0.0.1:3306/openproject
    SECRET_TOKEN=c5aa99a90f9650404a885cf5ec7c28f7fe1379550bb811cb0b39058f9407eaa216b9b2b22d27f58fb15ac21adb3bd16494ebe89e39ec225ef4627db048a12530

    ADMIN_EMAIL=mail@example.com
    EMAIL_DELIVERY_METHOD=smtp
    SMTP_DOMAIN=10.10.3.6
    SMTP_HOST=127.0.0.1
    SMTP_PASSWORD=mail
    SMTP_PORT=25
    SMTP_URL=smtp://mail:9a4itlkja49@10.10.3.6:25
    SMTP_ENABLE_STARTTLS_AUTO=true
    SMTP_AUTHENTICATION=plain
    SMTP_USERNAME=mail

    WEB_CONCURRENCY=2
    WEB_TIMEOUT=15

    RAILS_CACHE_STORE=memcache
    SESSION_STORE=cach_store

### Example change session store

    sudo service openproject-ce stop
    sudo openproject-ce config:set SESSION_STORE="active_record_store"
    sudo service openproject-ce start

_Attention:_ Be aware that you should only set or change settings which are directly related to the openproject application/instance itself (see the list above). For example settings related to the apache web server can also be set via the config:set option but this can end up in an undesired state of the openproject application. If you want to change these settings use the reconfigure option. This will bring up the installation wizard again. See the “Reconfigure via wizard” section for more details on that.

## Run commands like rake tasks or rails console

The openproject command line tool supports running rake tasks and known scripts like the rails console. See example below how to do that.

    sudo openproject-ce run rails console
    or rake task
    sudo openproject-ce run rake db:migrate

## Show logs

The command line tool can also be used to see the log information. The most typically use case is to show/follow all current log entries. This can be accomplished using the the –tail flag. See example below.

    sudo openproject-ce logs --tail

## Reconfigure via wizard
As already mentioned in section “Set configuration options” if it is necessary to reset all settings defined during the installation process the reconfigure option can be used.

    sudo openproject-ce reconfigure

The command above will bring up the installation wizard again. Please be aware that it will start the configuration/installation process from scratch. That is why it won’t show entries you already made. So you have to fill out every form again.

# Upgrade to a newer version

Upgrading the OpenProject CE is as easy as installing a newer OpenProject CE package and running the `openproject-ce configure` command.

## Debian / Ubuntu

    sudo apt-get update
    sudo apt-get install --only-upgrade openproject-ce
    sudo openproject-ce configure

## Fedora / CentOS / RHEL

    sudo yum update
    sudo yum install openproject-ce
    sudo openproject-ce

## SuSE Enterprise Server 12

    sudo zypper update openproject-ce
    sudo openproject-ce configure
