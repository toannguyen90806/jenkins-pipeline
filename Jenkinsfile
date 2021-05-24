def builderImage
def productionImage
def ACCOUNT_REGISTRY_PREFIX
def GIT_COMMIT_HASH

pipeline {
    agent any
    stages {
        stage('Checkout Source Code and Logging Into Registry') {
            steps {
                echo 'Logging Into the Private ECR Registry'
                script {
                    GIT_COMMIT_HASH = sh (script: "git log -n 1 --pretty=format:'%H'", returnStdout: true)
                    ACCOUNT_REGISTRY_PREFIX = "471959854276.dkr.ecr.us-east-1.amazonaws.com"
                    sh """
                    \$(aws ecr get-login --no-include-email --region us-east-1)
                    """
                }
            }
        }

        stage('Make A Builder Image') {
            steps {
                echo 'Starting to build the project builder docker image'
				sh 'docker build -t 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp-builder:0927603a6c981874dcab2217790a83bd7bf9d118 -f Dockerfile.builder .'
				
				echo 'docker push to example-webapp-builder'
				sh 'docker push 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp-builder:0927603a6c981874dcab2217790a83bd7bf9d118'
				
				sh 'docker run --rm -v "$PWD:/work" 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp-builder:0927603a6c981874dcab2217790a83bd7bf9d118 bash -c "cd /work; lein uberjar"'
				
				//echo "${ACCOUNT_REGISTRY_PREFIX}/example-webapp-builder:${GIT_COMMIT_HASH} -f ./Dockerfile.builder ."
				//sh 'docker build -t 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp-builder:0927603a6c981874dcab2217790a83bd7bf9d118 -f ./Dockerfile.builder .'
				
				/*
                script {
                    builderImage = docker.build("${ACCOUNT_REGISTRY_PREFIX}/example-webapp-builder:${GIT_COMMIT_HASH}", " -f ./Dockerfile.builder .")
                    echo 'Docker build image is OK'
					builderImage.push()
                    builderImage.push("${env.GIT_BRANCH}")
                    builderImage.inside('-v $WORKSPACE:/output -u root') {
                        sh """
                           cd /output
                           lein uberjar
                        """
                    }
                }
				*/
            }
        }

        stage('Unit Tests') {
            steps {
                echo 'running unit tests in the builder image.'
				sh 'docker run --rm -v "$PWD:/work" 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp-builder:0927603a6c981874dcab2217790a83bd7bf9d118 bash -c "cd /work; lein test"'
				/*
                script {
                    builderImage.inside('-v $WORKSPACE:/output -u root') {
                    sh """
                       cd /output
                       lein test
                    """
                    }
                }
				*/
            }
        }

        stage('Build Production Image') {
            steps {
                echo 'Starting to build docker image'
				sh 'docker build -t 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp:0927603a6c981874dcab2217790a83bd7bf9d118 -f Dockerfile.builder .'
				
				echo 'docker push to example-webapp'
				sh 'docker push 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp:0927603a6c981874dcab2217790a83bd7bf9d118'
				
				/*
                script {
                    productionImage = docker.build("${ACCOUNT_REGISTRY_PREFIX}/example-webapp:${GIT_COMMIT_HASH}")
                    productionImage.push()
                    productionImage.push("${env.GIT_BRANCH}")
                }
				*/
            }
        }

 
        stage('Deploy to Production fixed server') {
            when {
                branch 'release'
            }
            steps {
                echo 'Deploying release to production'
				sh 'docker push 471959854276.dkr.ecr.us-east-1.amazonaws.com/example-webapp:deploy'
				sh """
                       aws ec2 reboot-instances --region us-east-1 --instance-ids i-0f2a0b0d28bd6c44e
                    """
					
				/*
                script {
                    productionImage.push("deploy")
                    sh """
                       aws ec2 reboot-instances --region us-east-1 --instance-ids i-0f2a0b0d28bd6c44e
                    """
                }
				*/
            }
        }

/*
        stage('Integration Tests') {
            when {
                branch 'master'
            }
            steps {
                echo 'Deploy to test environment and run integration tests'
                script {
                    TEST_ALB_LISTENER_ARN="arn:aws:elasticloadbalancing:us-east-1:089778365617:listener/app/testing-website/3a4d20158ad2c734/49cb56d533c1772b"
                    sh """
                    ./run-stack.sh example-webapp-test ${TEST_ALB_LISTENER_ARN}
                    """
                }
                echo 'Running tests on the integration test environment'
                script {
                    sh """
                       curl -v http://testing-website-1317230480.us-east-1.elb.amazonaws.com | grep '<title>Welcome to example-webapp</title>'
                       if [ \$? -eq 0 ]
                       then
                           echo tests pass
                       else
                           echo tests failed
                           exit 1
                       fi
                    """
                }
            }
        }

 
        stage('Deploy to Production') {
            when {
                branch 'master'
            }
            steps {
                script {
                    PRODUCTION_ALB_LISTENER_ARN="arn:aws:elasticloadbalancing:us-east-1:089778365617:listener/app/production-website/a0459c11ab5707ca/5d21528a13519da6"
                    sh """
                    ./run-stack.sh example-webapp-production ${PRODUCTION_ALB_LISTENER_ARN}
                    """
                }
            }
        }
	*/
	
    }
}