pipeline{
    agent any
    stages{
        stage('CI'){
            steps{
                echo "CI successfull"  
            }
        }

        stage('CD'){
            steps{
                sh'''
                mv index.html /var/www/html
                ''' 
            }
        }
    }
}