# ![plant-simulator-ui](logo.png)

[![Codacy Badge](https://api.codacy.com/project/badge/Grade/996bef52feb148039c61f0db9cff9830)](https://www.codacy.com/app/joesan/plant-simulator?utm_source=github.com&amp;utm_medium=referral&amp;utm_content=joesan/plant-simulator&amp;utm_campaign=Badge_Grade)
[![codecov](https://codecov.io/gh/joesan/plant-simulator/branch/master/graph/badge.svg)](https://codecov.io/gh/joesan/plant-simulator)
[![Build Status](https://travis-ci.org/joesan/plant-simulator.svg?branch=master)](https://travis-ci.org/joesan/plant-simulator)
[![CII Best Practices](https://bestpractices.coreinfrastructure.org/projects/1185/badge)](https://bestpractices.coreinfrastructure.org/projects/1185)
[![Github All Releases](https://img.shields.io/github/downloads/joesan/plant-simulator/total.svg)]()
[![Powered By](https://img.shields.io/badge/poweredBy-Scala-brightgreen.svg)]()

##

# plant-simulator [![Open Source](https://img.shields.io/badge/Open%20Source-100%25-yellowgreen.svg)](https://github.com/ellerbrock/open-source-badges/)

## Getting Started 

For some background information on the project, please have a look [here](https://github.com/joesan/plant-simulator/wiki)

Here is what the operation of a PowerPlant would look like! A constant command and control of the PowerPlant would end up showing you the following graph!

![PowerPlant Command and Control](https://github.com/joesan/plant-simulator/blob/master/streaming_telemetry.png)

The instructions given below will get you a copy of the project up and running on your local machine for development and testing purposes. 

### Prerequisites

```
1. Install Java Version 8
2. Install Scala Version 2.11.8
3. Install IntelliJ - Latest community edition and then install the latest Scala plugin available
```

### Installing

Follow the steps below to import the project into IntelliJ

```
1. Clone the project from: 
   git clone https://github.com/joesan/plant-simulator.git
   
2. Fire up IntelliJ and import the project
   
3. If you are opening IntelliJ for the first time, set up the Scala library in IntelliJ
```

### Database Support

Though the application is multi database compliant, currently only MySQL and H2 databases are supported, the reason being that the [set up scripts](https://github.com/joesan/database-projects/tree/master/power-plant-simulator) are only avaiable for MySQL and H2. If you need support for other relational databases, please feel free to contribute. You only need to add the set up scripts and include the JDBC driver dependency to build.sbt. Everything else works out of the box!

You have to make sure to set up your database and configure the database credentials and url in one of the applicatiox.xxxx.conf files (where xxxx stands for either test or prod)

### Running tests

You have the option here to either run tests from IntelliJ or from the command line

To run tests from the command line, do the following:

```
1. Open a terminal and navigate to the project root folder 
   
2. Issue the following command: [Tests are run against an in memory H2 database and uses the application.test.conf]
   sbt clean test
```
To run any specific tests from within IntelliJ, simply right click the test that you wish you
run and click Run

### Running the application

This application is built as a web application using the Play framework. We have two options to run the application:

* Run as a standalone jar
* Run as a Docker container

#### To run as a standalone jar, do the following

```diff
+For simplicity, there is local H2 database setup (plant-simulator.mv.db). The connection details 
+are to be found under conf/application.dev.conf
   
+You can comfortably run the application using the following command (No WiFi, no Network required)
-sbt -Denv=dev run
```

If you want to run the application against a MySQL database, follow the instructione below:

```
0. First, we need a database that is up and running. Please have a look at
   this project that contains the setup script for the database (Supported databases are MySQL and H2):
   https://github.com/joesan/database-projects/tree/master/power-plant-simulator
   
   Make sure to have a running instance of your database server and to run the setup scripts.
   
   Once you have the database up and running, configure the database credentials in the 
   application.xxxx.conf file (xxxx stands for test or prod)
   
1. Open a terminal and navigate to the project root folder 
   
2. Issue one of the following commands to run:
   To run against a default (application.conf)
   sbt run
   
   To run against the qa environment
   sbt -Denv=qa run
   
   To run in production mode (application.prod.conf):
   sbt -Denv=prod -Dlogger.resource=logger-prod.xml run

3. Navigate to the following url on your favorite browser:
   http://localhost:9000
   
4. To do something meaningful with the application, have a look at the [documentation](https://github.com/joesan/plant-simulator/wiki/API-Documentation) for more information on how to call the API's!
   
``` 

To visualize the application and to do some real work with it, have a look [here](https://github.com/joesan/plant-simulator-ui) - Currently under development

Alternatively, you could visualize the app under the following URL:

`https://plant-simulator-ui.herokuapp.com/` - More information could be found [here](https://github.com/joesan/plant-simulator/wiki/API-Documentation)

#### To run as a Docker container

```
1. Makue sure you have Docker installed on your host system 
   
2. Issue one of the following commands to build the docker image:
   sbt docker:publishLocal
   
   The above command would have built the image and push it to your local
   docker registry (on your host system)
   
3. Issue the following command to run the container:
   TODO - Document
   
4. Navigate to the following url on your favorite browser:
   
   
   [TODO] Add the Swagger API Docs and put the URL here
   
```

## Load Testing the Application
I have included some possibilities with which you can load test the application. The idea is to create as many PowerPlant's as we want in the database and use this database to start the application. For convenience, I have created a script with which you can generate as many data as you want. The script db-generator.sh can be found [here](https://github.com/joesan/database-projects/tree/master/power-plant-simulator/h2). Have a look at the README.md on that link to know how you could generate the random massive data set.

For even more convenience, I have created alredy generated and pre-populated about 100,000 PowerPlant's in the H2 database file [plant-sim-load-test.mv.db](https://github.com/joesan/plant-simulator/blob/master/plant-sim-load-test.mv.db). All you need to do is just to run the application using this file as the database. So just issue the following command:

```
sbt -Denv=loadtest run
```

After you do this, I assure you that you will have fun! Ok so not you had some fun, let us have even more fun. Let us dispatch all of the PowerPlants at once. 
To do this I have prepared a simple script that can send dispatch commands. 

```
0. Make sure that the application is up and running and make sure that you have a WebSocket connection open
   To open a command line WebSocket client, you can do the following:
   npm install -g wscat
   wscat -c ws://localhost:9000/plantsim/powerplant/events
1. Open another terminal and navigate to the project rool folder
2. Call the dispatch script
   ./dispatch.sh
3. Make sure that you have some fun doing this!   
```


## Deployment

The application is pre-deployed on a Heroku server. More details cal be found (here)[https://github.com/joesan/plant-simulator/wiki/API-Documentation]

[[TODO]] Add additional notes about how to deploy this on a live system

[[TODO]] See [deployment](#Deployment) for notes on how to deploy the project on a live system.

## Tools Used

* [SBT](http://www.scala-sbt.org/) - Scala Build Tool

* [Travis CI](https://travis-ci.com/) - Hosted Continuous Integration

## Contributing [![contributions welcome](https://img.shields.io/badge/contributions-welcome-brightgreen.svg?style=flat)](https://github.com/joesan/plant-simulator/issues)

For more information on how to contribute, have a look [here](https://github.com/joesan/plant-simulator/blob/master/CONTRIBUTING.md)

## Authors / Maintainers

* *Joesan*           - [Joesan @ GitHub](https://github.com/joesan/)

See also the list of [contributors](https://github.com/joesan/plant-simulator/graphs/contributors) who helped.

## License [![License](https://img.shields.io/badge/License-Apache%202.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

The whole project is licensed under Apache License, Version 2.0. See [LICENSE.txt](./LICENSE.txt).

## Acknowledgments

* To everybody that helped in this project
* The [Monix library](https://monix.io/)

##
<sup>[![forthebadge](http://forthebadge.com/images/badges/powered-by-electricity.svg)](http://forthebadge.com)</sup>
<sup>[![forthebadge](http://forthebadge.com/images/badges/built-with-grammas-recipe.svg)](http://forthebadge.com)</sup>
<sup>[![forthebadge](http://forthebadge.com/images/badges/fuck-it-ship-it.svg)](http://forthebadge.com)</sup>
