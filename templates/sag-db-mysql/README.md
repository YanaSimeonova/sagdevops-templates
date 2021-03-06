<!-- Copyright 2013 - 2018 Software AG, Darmstadt, Germany and/or its licensors

   SPDX-License-Identifier: Apache-2.0

    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at

        http://www.apache.org/licenses/LICENSE-2.0

    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
     WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
     See the License for the specific language governing permissions and

     limitations under the License.                                                  

-->

# Creating webMethods database on MySQL server

> STATUS: INCUBATING

With this template you can create user, database, and webMethods database schemas on a MySQL Server.

## Requirements

### Supported Software AG releases

* Command Central 10.3 or higher
* Database Component Configurator 10.3
* webMethods database schemas for products version 10.3
* [MySQL JDBC Driver](https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-5.1.47.zip)

### Supported platforms

* All supported Windows and UNIX platforms
* All supported versions of MySQL Server database
* Docker Engine 17.09 or higher

## Running as a standalone Composite Template

To install Database Component Configurator 10.3 on the Command Central node with alias `local`,
create a database named `webm` and a database user named `webm` with password `webm`,
and create IS database product schemas with user `root` with password `root`:

```bash
sagcc exec templates composite apply sag-db-mysql \
  db.version=latest repo.product=products-10.3 repo.fix=fixes-10.3 nodes=local \
  db.host=mysql db.admin.username=root db.admin.password=root \
  db.name=webm db.username=webm db.password=webm \
  db.components=[STR] \
  db.products=[IS]
  --sync-job --wait 360
```

## Using for local development and testing on Docker platforms

Launch the Command Central container from the root folder of this project:

```bash
docker-compose up -d cc
```

Launch the [MySQL](https://hub.docker.com/_/mysql/) container:

```bash
docker-compose -f templates/sag-db-mysql/docker-compose.yml up -d mysql
```

Provision sag-db-mysql template and run tests:

```bash
CC_ENV=mysql ./provisionw sag-db-mysql
```

Successful test output looks like this:

```bash
...
**********************************
* Executing action
*   Action            : catalog
*   Database          : SQL Server
*   URL               : jdbc:mysql://mysql:3306/webm
*   User              : webm
**********************************

**********************************
*      Installed Components      *
**********************************
*   XRF 11 CrossReference
*   ISC 60 ISCoreAudit
*   ISI 75 ISInternal
**********************************

The expected values were successfully retrieved after 1 call within 4 seconds.
TEST SUCCESSFUL
```

You can now use this database for creating instances of webMethods products (Integration Server, My webMethods Server) with the following database connection properties:

```bash
db.url=jdbc:mysql://mysql:3306/webm
db.username=webm
db.password=webm
db.type=mysql
```

The properties are preset in `environments/mysql/env.properties` file and you can use them by poiting to the environment name. For example:

```bash
CC_ENV=mysql ./provisionw sag-is-cluster
```
