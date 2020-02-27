## NextGeoss API Docker development setup

This repository contains everything you need in order to run the NextGeoss data portal or to work on one of its extensions.
All the services are being run as docker images that come together in the docker-compose file.
They each have their own Dockerfile in their respective folder along with specific configuration files:

- [`/ckan`](ckan) - contains the Dockerfile for the basic CKAN along with crontab configurations for running the harvestes
- [`/postgresql`](postgresql) - contains the Dockerfile for PostgreSQL along with the init script for creating the database
- [`/solr`](solr) - contains the Dockerfile and configuration for Solr
- [`/src`](src) - contains the plugins that are being loaded at runtime


#### Using this repository

Clone this repository.
Run:

```
docker-compose -f docker-compose.dev.yml build
```

followed by:

```
docker-compose -f docker-compose.dev.yml up
```

This should give you a working docker development setup. 
The databases/indexes will be created for you according to the setting in `.dev.env` file. Feel free to change those setting as needed.

To destroy the images: 

```
docker-compose -f docker-compose.dev.yml down
```

Or if you also want to remove all the data and start afresh:

```
docker-compose -f docker-compose.dev.yml down --volumes
```

#### Installing plugins

Using this repo, there are 2 ways to install/uninstall plugins in CKAN:
- if you just want to install an existing plugin, edit [ckan/Dockerfile](ckan/Dockerfile) and install them with the RUN command via `pip`

E.g: `RUN pip install -e git+https://github.com/ckan/ckanext-googleanalytics.git#egg=ckanext-googleanalytics`

You can switch brances, repo and so on as you would in a pip requirements file. 

- if you are developing a new plugin, you can clone it into [`/src`](src) and it will be installed automatically when you run `docker-compose up`, along with any dependencies it may have in its `requirements.txt` file.

#### Using plugins

Once the plugins are installed, you need to add them to the `CKAN_PLUGINS` environment variable in order to make sure they are being loaded.
The order matters because they are being installed in the order you enummerate them and that influences how the templates are being overwritten. More details on that in [this section of the CKAN docs](https://docs.ckan.org/en/2.8/maintaining/configuration.html#ckan-plugins)
This is the order we use:

```
CKAN__PLUGINS=envvars stats image_view text_view recline_view spatial_metadata googleanalytics opensearch spatial_query harvest ckan_harvester grouphierarchy nextgeoss kpis dcat dcat_json_interface admintools esa cmems gome2 foodsecurity gdacs modis deimosimg  [...other_harvesters]
```





