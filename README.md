# About API Gateway Devops

As each organization builds APIs using API Gateway for easy consumption and monetization, the continuous integration and delivery are integral part of the API Gateway solutions to get hold of the fast moving market. We need to automate the management of APIs and policies to speed up the deployment, introduce continuous integration concepts and place API artifacts under source code management. As new apps are deployed, the API definitions can change and those changes have to be propagated to other external products like API portal. This requires the API owner to update the associated documentation and in most cases this process is a tedious manual exercise. In order to address this issue, it is a key to bring in DevOps style automation to the API life cycle management process in API Gateway. With this, enterprises can deliver continuous innovation with speed and agility, ensuring that new updates and capabilities are automatically, efficiently and securely delivered to their developers and partners in a timely fashion and without manual intervention. This enables a team of API Gateway policy developers to work in parallel developing APIs and policies to be deployed as a single API Gateway configuration.

![GitHub Logo](/images/api.png)


## API Gateway assets and configurations
The following are the  API Gateway assets and configurations that can be moved across API Gateway stages.
 - Gateway APIs 
 - Policy Definitions/Policy Templates/Global Policies 
 - Applications
 - Aliases
 - Plans 
 - Packages
 - Subscriptions 
 - Users/Groups/ACLs/Teams
 - General Configurations like Load balancer,Extended settings
 - Security configurations 
 - Destination configurations
 - External accounts configurations

## API Gateway Deployment
API Gateway can be deployed with many flavors.
 - Standlone API Gateway with embedded elastic search.
 - Clustered API Gateway with embedded elastic search.
 - Standalone API Gateway with external elastic search and Kibana.
 - Clustered API Gateway with external elastic search and Kibana.
 
 The docker compose files for these different deployment styles can be found at https://github.com/SoftwareAG/webmethods-api-gateway/tree/master/samples/docker/deploymentscripts
 
 ## Devops Automation and CI/CD in API Gateway
 The CI/CD and devops flow can be acheived in multiple ways. 
 ### Using webMethods Deployer and Asset Build environment
  API Gateway asset binaries can be build using Asset Build environment and promoted across stages using WmDeployer. More information on this way of CI/CD and Devops automation can be found at http://techcommunity.softwareag.com/pwiki/-/wiki/Main/Staging%2C+Promotion+and+DevOps+of+API+Gateway+assets 
 ### Using API Gateway promotion management.
  The promotion APIs that are exposed by API Gateway can be used for the devops automation. More information on these APIs can be found at https://github.com/SoftwareAG/webmethods-api-gateway/blob/master/apigatewayservices/APIGatewayPromotionManagement.json

# About this repository

This repository is a sample repository for someone to get started with a DevOps flow for API Gateway assets.
One can clone this repository then modify it to suite their organizational needs.
The samples in this repository use the API Gateway Promotion APIs for automation of the Devops flow.

The repository has the following folders

  - apis : Contains the API Gateway assets of the organization 
  - configuration : Contains staged specfic API Gateway Administration configurations.
  - deployment-descriptor : Contains staged specific docker compose files to create API Gateway environments.
  - tests : Contains the Postman Test suites and Environmental variables required for the test suites.
  - utilities : Contains the Postman collections for Import/Export, Promotion management.
  - bin : Library scripts that are used to create API Gateway environments, Develop assets and test them.
  - pipelines : Contains some sample Jenkins and the Azure pipelines as an insight for organizations. 
  
# Devops CI/CD usecases

This github project contains the scripts that can perform some of the basic Devops and automation 
of APIs using API Gateway.

## Creating Staged API Gateway environment
 
 Organizations will have to create a staged API Gateway environment which would help them with agile
developement of APIs and continious delivery. A typical organization will have a Dev,QA and Prod environments of 
API Gateway.
 This Github project contains some sample deployment descriptors under /deployment-descriptors which are 
docker compose files to create staged API Gateways.
By default the deployment-descriptors has the following configurations.
 - build enviroment that creates a single-node API Gateway. This is mostly for building APIs in a developer machine and assert them with the tests.
 - dev enviroment that creates a single-node API Gateway.
 - qa and prod environements which are three node clustered API Gateways with a ng-inx load balancer.

More examples with different flavors of deployment can be found at  
https://github.com/SoftwareAG/webmethods-api-gateway/tree/master/samples/docker/deploymentscripts. 


### The gateway-setup.sh
The gateway-setup.sh under /bin can be uses these deployment descriptors and create the environments.

| Parameter | Description |
| ------ | ------ |
| stage |  Possible value are build,dev,qa,prod. |
| apigateway_image | The DTR for API Gateway image. |
| terracotta_image | The DTR for Terracotta image in case of cluster environments |
| apigateway_server_port | API Gateway server port.Default is 5555 |
| apigateway_ui_port | API Gateway UI port.Default is 9072 |
| apigateway_es_port |  API Gateway Elastic search port.Default is 9240 |
| create_new | Create new API Gateway container even if an  existing container is running by killing it.Default is false | 
| import_configurations | Import configurations into the created container|
| cleanup | Cleanup the created images |

Sample usage to create a prod environment
```sh
 $ gateway-setup.sh --stage prod  --apigateway_image mycompany_apigateway_image:latest --terracotta_image mycompany_terracotta_image:latest --create_new true 
```
To clean up the created environment
```sh
 $ gateway_setup.sh --stage dev --cleanup
```

More examples of deployment-descriptors with different flavors of API Gateway deployment can be found at  https://github.com/SoftwareAG/webmethods-api-gateway/tree/master/samples/docker/deploymentscripts.
One can clone this repository and add their staging environments under /deployment-descriptors  and use them in the scripts.

# Develop APIs and test
The gateway_import_export_utils.sh can be used for developers import and export APIs(projects)  as a flat file representation of the VCS.

# Parameters
| Parameter | README |
| ------ | ------ |
| import(or)export |  To import or export from the flat file. |
| api_name | The API project to import.|
| apigateway_url |  APIGateway url to import or export from.Default is http://localhost:5555.|
| apigateway_es_port | API Gateway Elastic search port.Default is 9240|
| username |  The APIGateway username.Default is Administrator.|
| password | The APIGateway password.Default is password.|

Sample Usage for importing the API petstore that is present as flat file under apis/petstore into API Gateway server at http://localhost:5555 

```sh
 $ gateway_import_export_utils.sh  --import --api_name petstore --apigateway_url http://localhost:5556
```
Sample Usage for exporting the API petstore that is present in the  API Gateway server at http://localhost:5555  as flat file under apis/petstore

```sh
 $ gateway_import_export_utils.sh  --export --api_name petstore --apigateway_url http://localhost:5556
```

There is also a build script gateway_build.sh that can be used for developers to validate the changes made to the APIs. This creates an single node Docker based API Gateway environment 
imports the entire set and of API Projects and then run the tests suites 
against the build machine.

# Parameters
| Parameter | Description |
| ------ | ------ |
| apigateway_image | The DTR for API Gateway image. |
| apigateway_server_port | API Gateway server port.Default is 5555 |
| apigateway_ui_port | API Gateway UI port.Default is 9072 |
| apigateway_es_port |  API Gateway Elastic search port.Default is 9240 |
| create_new | Create new API Gateway container even if an  existing container is running by killing it.Default is false | 
| test_suite |  The postman collection test_suite to run. Default will not run any test.To run all tests pass *|
| skip_import | To skip the import of APIs |

Sample usage to run all tests by creating an build environment
``` sh 
gateway_build.sh --apigateway_image mycompany_apigateway_image:latest --apigateway_server_port 5558 --apigateway_ui_port 9075  --apigateway_es_port 9243 --test_suite \*
```

Sample usage to run only a subset of tests
``` sh 
gateway_build.sh --apigateway_server_port 5558 --test_suite ../tests/test-suites/APITest.json
```

# Pipelines
This repository contains a sample Jenkins and Azure pipline that can be used for the Continious integration process.
These pipelines depicts how an API(project) that is present in VCS can be promoted to an API Gateway Prod environment  after testing it in the  API Gateway QA environment. The API(project) is imported in the QA environment and after running tests it is promoted to the Prod using the Promotion mangement APIs.  


