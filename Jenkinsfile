node {
    stage('Preparation') {
        checkout scm
    }

    stage('Restore') {
        sh 'dotnet restore Rise.sln'
    }

    stage('Build') {
        sh 'dotnet build Rise.sln --configuration Release --no-restore'
    }

    stage('Test') {
        sh 'dotnet test Rise.sln --configuration Release --no-build'
    }

    stage('Docker Build') {
        sh 'docker build -t rise-app:${BUILD_NUMBER} .'
    }

    stage('Deploy') {
        withCredentials([
            string(
                credentialsId: 'rise-db-connection',
                variable: 'DB_CONNECTION'
            )
        ]) {
            sh '''
                docker rm -f rise-app 2>/dev/null || true

                docker run -d \
                    --name rise-app \
                    --restart unless-stopped \
                    --network rise-net \
                    -p 5000:5000 \
                    -e ASPNETCORE_ENVIRONMENT=Production \
                    -e ASPNETCORE_URLS=http://+:5000 \
                    -e "ConnectionStrings__DatabaseConnection=$DB_CONNECTION" \
                    rise-app:${BUILD_NUMBER}
            '''
        }
    }

    stage('Verify Deployment') {
        sh '''
            sleep 5
            docker ps --filter name=rise-app
            docker logs --tail 50 rise-app
        '''
    }
}