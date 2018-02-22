Assumptions:
1. Azure Resource Group and Application Service Plan is already created.
2. Code is stored in an on-site repository.
3. Code is written in node.
4. Test scenarios are pre-written.


**SERVICE CREATION on AZURE**


- deployservice.sh
    - Deploys an new Application Service on Azure using the template.json and parameters.json
    - If you have no existing service, please run this without modifying template.json and parameters.json
    - Ensure that template.json and parameters.json are in the same directory you are running deployservice.sh

- template.json
    - Contains the template for the following:
        - Service configuration
        - Server farm configuration
        - Auto scaling configuration
        - Application configuration
        - Application version(default "APP_ENV": "develop")
    - Default parameters are set to deploy a test/develop version of the application

- parameters.json
    - Can be used to override default values in template.json if deploying a different version/instance of the application service


**CODE TESTING and DEPLOYMENT**


- pipeline.yaml
    - Source repository is defined in the source parameter. Branches/tags can be set in the ref parameter.
    - Calls Jenkinsfile within the repository to kick the pipeline
    - Import the pipeline to your exsiting OpenShift project/namespace using `oc create -f pipeline.yaml`
    - To start the pipeline, run `oc start-build deploy-pipeline`

- Jenkinsfile
    - Copy this Jenkisfile into the root directory of your application repository.
    - This is Multi-stage pipeline to deploy code from the local git repository to the Azure repository.
    - Externalized to allow multiple iterations with varying parameters. 
    - There is also the option to emben this in-line in the pipeline.
    - Running unit test is built in to the `'Test'`
    - Any other testing can be inserted into the `'Test'` stage



**SWITCHING APPLICATION VERSIONS on AZURE**


- New APPLICATION SERVICE
    - Change `"APP_ENV":` parameter in template.json. Default is develop.

- Existing APPLICATION SERVICE
    - You are able to switch application versions without redeploying the code.
    - Run the following in Azure CLI

  `az webapp config appsettings set -g <MyResourceGroup> -n <AppServiceName> --settings APP_ENV=<app_env>`
