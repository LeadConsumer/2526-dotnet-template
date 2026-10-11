node {
    stage('Preparation') {
        // Haal de nieuwste code binnen uit je GitHub fork
        checkout scm
    }
    
    stage('Restore') {
        // Herstel alle benodigde NuGet-pakketten voor de solution
        sh 'dotnet restore Rise.sln'
    }

    stage('Build') {
        // Bouw de volledige applicatie in Release-modus zonder opnieuw te restoren
        sh 'dotnet build Rise.sln --configuration Release --no-restore'
    }

    stage('Test') {
        // Voer automatisch alle aanwezige xUnit en bUnit tests uit
        sh 'dotnet test Rise.sln --configuration Release --no-build'
    }
}
