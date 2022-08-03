# OnSite WebCSD

OnSite WebCSD is currently only available to CCDC Research Partners and other invited groups.

This release is an Alpha release aimed at getting feedback on the installation and update process.

This alpha release does not contain all the functionality currently provided in On-Site WebCSD. All functionality will be provided in the full release.

## Prerequisites

A standard [Docker Server](https://docs.docker.com/engine/install/#server) and [docker-compose](https://docs.docker.com/compose/install/) installation is required for installation. This will run on [Docker Desktop](https://docs.docker.com/engine/install/#desktop), however this is not recommended and may [require a license](https://www.docker.com/legal/docker-subscription-service-agreement/).

Access to the CCDC container registry will require a username and password that will be provided by CCDC Support.

A valid license key will be required to use the software.

A download of the desired [release](https://github.com/ccdc-opensource/onsite-webcsd/releases) or a clone of this repository (this will require a [git installation](https://git-scm.com/downloads)).

```
git clone https://github.com/ccdc-opensource/onsite-webcsd.git
```

## Initial recommended specification

This is our initial estimate of what will be required to run WebCSD well.
170GB free hd space, 32GB ram, 8 core cpu, Linux OS

## Installation

After cloning the repository onto the server which the software will be installed you will need to go into the onsite-webcsd directory and copy the environment file `sample.env` as `.env` then populate with suitable values.

```
cd onsite-webcsd
cp sample.env .env
```

The values in the .env file are as follows and here are some examples of how it should look:

```
CCDC_LICENSING_CONFIGURATION='la-code;123456-123456-123456-123456-123456-123456;'
CSD_DB_PASSWORD='A password of your choosing'
CSD_CACHE_PASSWORD='A password of your choosing'
```

Where stated, some of these variables will be provided by CCDC; all other variables are for the user to generate and set. Once the variables file has been populated, login to the CCDC container registry and bring up the stack:

```
docker login -u <user> -p <password> ccdcrepository.azurecr.io

# or to be prompted for the password
docker login -u <user> --password-stdin ccdcrepository.azurecr.io

docker-compose up -d
```

### Offline Installation

This release will be only available online, if this is a problem please contact us. In the future we plan on supporting offline installs.

## Updates

When you have been notified when there is an update available, checkout the tag for the release.

```sh
cd <onsite installation directory>
git pull

# Optionally
git checkout tags/<specific release tag>
```

If the installation files have been downloaded from the release page you will need to download the latest version again from there and ensure the default old files have been removed and any custom configuration files have been moved to the new release directory.

Once the latest installation files have been obtained, to update the software, pull the latest images, and restart the stack. The latest images can be pulled whilst the stack is running and changes will only come into effect upon restarting the stack.

```sh
docker-compose pull

docker-compose down
docker-compose up -d
```

Note: if custom databases have been defined using an override file, use the command shown in the [Local Database Configuration](#local-database-configuration) section.

## Verifying the Installation/Update

To check that the install or update has completed and that all the services are running, run:

```sh
docker-compose ps
```

which should show the state of the services to all be `Up`. If any services have the state `Up (unhealthy)` or `Exit` then restart the stack. If the issues persist then please contact CCDC Support.

During the Alpha release some of these services will be unhealthy - this is expected and will be fixed before the final release.
The services which will be unhealthy are:

```
onsite-webcsd_ccdc-csd-searchservice_1
onsite-webcsd_ccdc-csd-resultstore_1
```

## Local Database Configuration

On-Site WebCSD can be configured to read from local databases. To enable these databases, copy and rename the file `docker-compose.sample-db-config.yml` to `docker-compose.db-config.yml` and edit the `volumes` section to point to any local databases and edit the `environment` section to configure the application to recognise these databases. More information is given in the notes & example sections of the sample file. This acts as an [override file](https://docs.docker.com/compose/extends/) which you will have to include in the startup command:

```
docker-compose -f docker-compose.yml -f docker-compose.db-config.yml up -d
```

For more information see the [Docker volumes documentation](https://docs.docker.com/compose/compose-file/#volumes).

## Usage

To access the WebCSD service locally go to http://localhost in a browser.

## Contact support

If you experience any difficulties with installing or using OnSite WebCSD, please contact our support team at support@ccdc.cam.ac.uk who will be happy to assist you.
