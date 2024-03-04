# Onsite Lattice and WebCSD

Onsite Lattice and WebCSD is currently only available to CCDC industrial customers and other invited groups.

This readme contains information required for installation and updates of Onsite Lattice and WebCSD.

Please use the latest version from the release tab <https://github.com/ccdc-opensource/on-site-webcsd/releases>.

Terms and conditions can be found in the license.md file.

Additional information including user configuration and troubleshooting can be found in the wiki at <https://github.com/ccdc-opensource/on-site-webcsd/wiki>.

## Prerequisites

A standard [Docker Server](https://docs.docker.com/engine/install/#server) and [Docker Compose](https://docs.docker.com/compose/install/) installation is required for installation.
This will run on [Docker Desktop](https://docs.docker.com/engine/install/#desktop), however this is not recommended and may [require a license](https://www.docker.com/legal/docker-subscription-service-agreement/).

Access to the CCDC container registry will require a username and password, to get them please contact CCDC Support.

For version 4.0 onwards, a postgres server is required for CSD and Identity databases. The postgres CSD database will be provided via a download link, to get it, please contact CCDC Support.

A valid license key will be required to use the software.

A download of the desired [release](https://github.com/ccdc-opensource/on-site-webcsd/releases).
Click on the release you want to use, and download the source code zip and unpack.

## Initial recommended specification

Recommended requirements for Onsite Lattice and WebCSD:
170GB free hd space, 32GB ram, 8 core cpu.

Recommended requirements for Onsite Lattice and WebCSD including Macromolecule Hub:
400GB free hd space, 32GB ram, 8 core cpu.

Onsite Lattice and WebCSD should work with any linux OS that meets the requirements to run Docker, but official support is provided by CCDC on the following platforms:

- Linux - Intel compatible, 64-bit:
  - RedHat Enterprise 7.6 or higher, and 8
  - CentOS 7.6 or higher
  - CentOS Stream 8
  - Ubuntu LTS 20 and 22

## In-house Database Configuration

Onsite Lattice and WebCSD can be configured to read from in-house databases.
To enable these databases follow:

1. Copy and rename the file `docker-compose.sample-onsite-only-db-config.yml` to `docker-compose.db-config.yml`
2. Edit the `volumes` section of that file to point to any in-house databases and edit the `environment` section to configure the application to recognise these databases.

More information is given in the notes & example sections of the sample file. This acts as an [override file](https://docs.docker.com/compose/extends/) which you will have to include in the startup command.

## CSD-Theory Web Database Configuration

Instructions on setting up CSD-Theory Web can be found in the [wiki](https://github.com/ccdc-opensource/on-site-webcsd/wiki/Setting-up-CSD%E2%80%90Theory-Web)

## Installation

After unpacking the release source code onto the server on which the software will be installed you will need to go into the on-site-webcsd directory and copy the environment file `sample.env` as `.env`.
Then open the `.env` file and populate with suitable values.

```console
cd on-site-webcsd
cp sample.env .env
```

You will need to update the .env file with your licence key and the two passwords you would like to use.
Here is an example of the .env file:

```
CCDC_LICENSING_CONFIGURATION=la-code;123456-123456-123456-123456-123456-123456;
CSD_DB_CONNECTIONSTRING=Server=database-server;Port=5432;Database=csd-database;User Id=postgres;Password=passwordhere
WEBCSD_PORT=80
```

Where stated, some of these variables will be provided by CCDC; all other variables are for the user to generate and set.
Once the variables file has been populated, login to the CCDC container registry and bring up the stack:

```console
docker login -u <user> -p <password> ccdcrepository.azurecr.io

# or to be prompted for the password
docker login -u <user> --password-stdin ccdcrepository.azurecr.io

# As of v2.0.0, containers run as non root users. Because of this you will need to run the following in the on-site-webcsd directory:
sudo adduser ccdc
sudo chown -R ccdc:ccdc userdata/

# You will also need to ensure the user "ccdc" has read access to any in-house or CSP databases by using the command above on relevant directories. 

#Use one of the following commands: 

#Use this command if you have no in-house databases and don't want to use macromolecule hub
docker compose up -d

#Use this command if you have in-house databases and don't want to use macromolecule hub 
docker compose -f docker-compose.yml -f docker-compose.db-config.yml up -d

#Use this command if you have in-house databases and want macromolecule hub 
docker compose -f docker-compose.yml -f docker-compose.macromolecule-hub.yml -f docker-compose.db-config.yml up -d

#Use this command if you have no in-house databases and want macromolecule hub
docker compose -f docker-compose.yml -f docker-compose.macromolecule-hub.yml up -d
```

## Updates

When you have been notified when there is an update available you can download the newest release from the github repository.
Oce you have downloaded the new release, ensure the default old files have been removed and any custom configuration files have been moved to the new release directory.

Once the latest installation files have been obtained, to update the software, pull the latest images, and restart the stack.
The latest images can be pulled whilst the stack is running and changes will only come into effect upon restarting the stack.

```sh
docker compose pull

docker compose down
#Use one of the following commands: 

#Use this command if you have no in-house databases and don't want to use macromolecule hub
docker compose up -d

#Use this command if you have in-house databases and don't want to use macromolecule hub 
docker compose -f docker-compose.yml -f docker-compose.db-config.yml up -d

#Use this command if you have in-house databases and want macromolecule hub 
docker compose -f docker-compose.yml -f docker-compose.macromolecule-hub.yml -f docker-compose.db-config.yml up -d

#Use this command if you have no in-house databases and want macromolecule hub
docker compose -f docker-compose.yml -f docker-compose.macromolecule-hub.yml up -d
```

## Verifying the Installation/Update

To check that the install or update has completed and that all the services are running, run:

```sh
docker compose ps
```

which should show the state of the services to all be `Up`. If any services have the state `Up (unhealthy)` or `Exit` then restart the stack. If the issues persist then please contact CCDC Support.

For more information see the [Docker volumes documentation](https://docs.docker.com/compose/compose-file/#volumes).

## Usage

To access the On-Site Lattice and WebCSD service locally go to <http://localhost> in a browser.

## Contact support

If you experience any difficulties with installing or using On-Site Lattice and WebCSD, please contact our support team at <support@ccdc.cam.ac.uk> who will be happy to assist you.
