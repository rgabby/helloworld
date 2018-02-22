Assumptions:
Azure Resource Group and Application Service Plan is already created.

Code is stored in an on-site repository.

Code is written in node.

Unit test is pre-written.


SERVICE CREATION


deployservice.sh
- Deploys an new Application Service on Azure using the template.json and parameters.json
- If you have no existing service, please run this without modifying template.json and parameters.json

template.json
- Contains the template for the following:
    Service configuration
    Server farm configuration
    Auto scaling configuration
    Application configuration
    Application version(default "APP_ENV": "develop")
- Default parameters are set to deploy a test/develop version of the application

parameters.json
- Can be used to override default values in template.json if deploying a different version/instance of the application service


CODE TESTING and DEPLOYMENT


pipeline.yaml
- Source repository is defined in the source parameter. Branches/tags can be set in the ref parameter.
- Calls Jenkinsfile within the repository to kick the pipeline

Jenkinsfile
- Multi-stage pipeline to deploy code from the local git repository to the Azure repository
- Running unit test is built in to the stages
- Externalized to allow multiple iterations with varying parameters


SWITCHING APPLICATION VERSIONS


New APPLICATION SERVICE
- Change "APP_ENV": parameter in template.json. Default is develop.

Existing APPLICATION SERVICE
- You are able to switch application versions without redeploying the code.
- Run the following in Azure CLI

  az webapp config appsettings set -g MyResourceGroup -n AppServiceName --settings APP_ENV=6.9.1
