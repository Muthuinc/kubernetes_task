pipeline{
    agent any
    stages{
        stage('CI'){
            steps{
                echo "CI successfull"  
            }
        }
    }
     stages{
        stage('CD'){
            steps{
                sh'''
                sudo mv index.html /var/www/html
                ''' 
            }
        }
    }
}