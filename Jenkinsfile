pipeline {
    agent any
	environement {
	SERVER_URL = "http://localhost:3000"
	NUCLEI_TEMPLATES_PATH = "/home/kali/.local/nuclei-templates"
}
    stages {
        stage('Cloner le dépôt') {
            steps {
                git branch: 'main', url: 'https://github.com/korgrunt/vuln-server' // Remplace par l'URL du dépôt
            }
        }

        stage('Install dependances et start serveur') {
	steps{
	sh '''
	npm install
	npm start &
	sleep 15
	'''
	}
	}
	
	stage('lancer analyse dyn avec nuclei') {
	steps {
	sh '''
	nuclei -u ${SERVER_URL} -t ${NUCLEI_TEMPLATES_PATH}-o report-nuclei.txt


	'''
	}
	}


	stage('Résultats Nuclei") {
	steps {
	script {
	def findingsFile = './report-nuclei.txt'
	def findingsCheck = sh(script: "grep -i 'high\\|medium\\|low' $(findingsFile)", returnStatus: true)
	// si des vuln sont trouvées, echec build
	if (findingsCheck == 0) {
	error("Des vunérabilités sont présentes dans le scan")       
	}
	}
	}
	}
        }
        
    post {
        always {
            cleanWs()  // Nettoyer le workspace après l'exécution
        }
    }
}
          
