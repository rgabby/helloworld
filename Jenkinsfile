node('node') {

    deploy.result = "SUCCESS"

    try {

    stage('Checkout'){
        checkout scm
        }

    stage('Build'){
        sh '''
           npm install
           npm run build:prod
           npm prune --production
           '''
        }

    stage('Test'){
        sh 'npm test'
        }

    stage ('Deploy'){
        sh '''
           export AZURE_APP_NAME=svctest
           export AZURE_GIT_SECRET_USERNAME=$AZURE_GIT_USERNAME
           export AZURE_GIT_SECRET_PASSWORD=$AZURE_GIT_PASSWORD
           git config --global credential.helper cache
           git clone https://$AZURE_GIT_SECRET_USERNAME:$AZURE_GIT_SECRET_PASSWORD@${AZURE_APP_NAME}.scm.azurewebsites.net:443/${AZURE_APP_NAME}.git
           rm -rf $AZURE_APP_NAME/*
           cp -r dist/* ${AZURE_APP_NAME}/
           cd ${AZURE_APP_NAME}/
           git config --global user.email "builder@email.com" && git config --global user.name "Builder"
           git add -A && git commit -m "Deploying new code, $(date)"
           git push origin master
           '''
        }

    }

    catch (err) {

    deploy.result = "FAILURE"

        mail body: "BUILD ERROR: ${env.BUILD_URL}" ,
        from: 'jenkinsbuilder@build.com',
        replyTo: 'jenkinsdeployer@build.com',
        subject: 'BUILD FAILED',
        to: 'ops@build.com'

    throw err
    }
}
