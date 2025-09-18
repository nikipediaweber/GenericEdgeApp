# Generic Industrial Edge App - App Developer Guide

Creating a first Industrial Edge App on a development environment to deploy it to an Industrial Edge Device based on App Developer Guide.

- [Generic Industrial Edge App - App Developer Guide](#generic-industrial-edge-app---app-developer-guide)
  - [Prerequisites](#prerequisites)
  - [Installation](#installation)
  - [Description](#description)
  - [Documentation](#documentation)
  - [Testing](#testing)
  - [Contribution](#contribution)
  - [License and Legal Information](#license-and-legal-information)
  - [Disclaimer](#disclaimer)

## Prerequisites

Prerequisites are in the App Developer Guide which is available on [industrial-edge.io](https://docs.eu1.edge.siemens.cloud/develop_an_application/developer_guide/00_Overview.html). It contains description of the requirements as well as the step-by-step description how to work with this Developer Guide repository.

## Installation

If you would like to run the solution of this app you need to rename all files called "Dockerfile.example" to Dockerfile. These Dockerfiles are just an example how you could implement it.

## Description

As the example app will cover the most common use case in the Industrial Edge environment, the app on the Industrial Edge Device will look like the architectural overview in the figure below. The goal of the app will be to collect, process and store data from an OPC UA Server, which provides data from a PLC.

![Overview of app architecture](./docs/Picture_5_3_Architecture_IED.png)

The app contains three parts – the connectivity to collect the data from the OPC UA Server by system apps, the IE Databus for distributions of the data and the process, storing and visualization of data in the Edge App.

1. The **IE Databus** based on MQTT is responsible for distributing data to certain topics, that are filled by system or custom apps by publishing and subscribing to these topics.
2. To receive the data from the OPC UA server, which is providing data from a PLC, the **OPC UA Connector connectivity** is used. OPC UA Connector is a system app, that publishes the data to IE Databus. Another system app, the SIMATIC Flow Creator, consumes the data from the OPC UA Connector topics on the IE Databus. The data is preprocessed in the SIMATIC Flow Creator before being published on the IE Databus again.
3. The developed **data analytics container** with Python is consuming the preprocessed data on the topics from the SIMATIC Flow Creator. The Python data analytics performs calculations and evaluations and returns the results as KPIs back to the IE Databus. To handle the IE Databus publishes and subscriptions, the data analytics container requires a MQTT client.
4. The **SIMATIC Flow Creator** consumes the analyzed data again. The SIMATIC Flow Creator persistently stores the (raw) and analyzed data in InfluxDB.
5. The **InfluxDB** is a time series database which is optimized for fast, high-availability storage and retrieval of time series data. It stores both the data transmitted by the OPC UA server to the app and the analyzed data.
6. The data stored in the database can be queried and graphed in dashboards to format them and present them in meaningful and easy to understand way. There are many types of dashboards to choose from including those that come with InfluxDB or other open source projects like Grafana. In this application, the native **InfluxDB Dashboards** are leveraged for basic data visualization.

## Documentation

- Here is a link to the [industrial-edge.io](https://docs.eu1.edge.siemens.cloud/develop_an_application/developer_guide/00_Overview.html) where the App Developer Guide of this application example can be found.
- You can find further documentation and help in the following links
  - [Industrial Edge Hub](https://iehub.eu1.edge.siemens.cloud/#/documentation)
  - [Industrial Edge Forum](https://www.siemens.com/industrial-edge-forum)
  - [Industrial Edge landing page](http://siemens.com/industrial-edge)

## Testing

To setup the test-environment for the development of the Industrial Edge App start a Linux virtual Machine or wsl inside the repository.

````PS C:\Repositories\Siemens\GenericEdgeApp\src> wsl````

If there are still some containers running it is advisable to stop and close them so that the system is clean:

Stop all containers:

````docker stop $(docker ps -a -q)````


Close all containers:

````docker rm $(docker ps -a -q)````

When youve started wsl or another linux system, open several Tabs of the command line in the different containers folders (my_edge_app, mqtt_boker_mosquitto, node_red) and in each of them first run the ``docker-compose build`` and afterwards the ``docker-compose up -d`` command.

This will compose the docker container according to the docker-compose.yml - file that is stored with each of the containers.

When this is done for all containers, you can run the following command to see which containers are running:

````docker ps````

This should show:

````
CONTAINER ID   IMAGE                      COMMAND                  CREATED          STATUS          PORTS                                           NAMES
0e5d1b0406a4   eclipse-mosquitto:1.6.14   "/docker-entrypoint.…"   16 seconds ago   Up 15 seconds   0.0.0.0:33083->1883/tcp, [::]:33083->1883/tcp   ie-databus
49feda5f2f7d   influxdb:2.4-alpine        "/entrypoint.sh infl…"   23 seconds ago   Up 22 seconds   0.0.0.0:38086->8086/tcp, [::]:38086->8086/tcp   influxdb
04c507901b36   data-analytics:v0.0.1      "python -u -m app"       23 seconds ago   Up 22 seconds                                                   data-analytics
4e9eb4beca1e   nodered:v0.0.1             "docker-entrypoint.s…"   29 seconds ago   Up 29 seconds   0.0.0.0:33080->1880/tcp, [::]:33080->1880/tcp   nodered
````

When all containers are running correctly InfluxDB and NodeRed can be started to configure the Dashboard and data traffic.

The links to start the are:

NodeRed: http://localhost:33080

InfluxDB: http://localhost:38086

As configured in their respective dockerfiles and compose.yml files.
  
For example the NodeRed-Container is Exposed at port 1880 as can be seen in its Dockerfile:

````docker
# User configuration directory volume
EXPOSE 1880
````
Afterwards this port is rerouted to the Host-Port of 33080 in the compose.yml-file:

```` docker
    ports:                                    # expose of ports and publish
      - "33080:1880"                          # map containers port 33080 to host's port 1880
````


  
## Contribution

Thank you for your interest in contributing. Anybody is free to report bugs, unclear documentation, and other problems regarding this repository in the Issues section.
Additionally everybody is free to propose any changes to this repository using Pull Requests.

If you haven't previously signed the [Siemens Contributor License Agreement](https://cla-assistant.io/industrial-edge/) (CLA), the system will automatically prompt you to do so when you submit your Pull Request. This can be conveniently done through the CLA Assistant's online platform. Once the CLA is signed, your Pull Request will automatically be cleared and made ready for merging if all other test stages succeed.

## License and Legal Information

Please read the [Legal information](LICENSE.txt).

## Disclaimer

IMPORTANT - PLEASE READ CAREFULLY:

This documentation describes how you can download and set up containers which consist of or contain third-party software. By following this documentation you agree that using such third-party software is done at your own discretion and risk. No advice or information, whether oral or written, obtained by you from us or from this documentation shall create any warranty for the third-party software. Additionally, by following these descriptions or using the contents of this documentation, you agree that you are responsible for complying with all third party licenses applicable to such third-party software. All product names, logos, and brands are property of their respective owners. All third-party company, product and service names used in this documentation are for identification purposes only. Use of these names, logos, and brands does not imply endorsement.
