pipeline {
    agent any
    
    environment {
        NOM_PROJET = 'nouveau-pipeline-groupe'
        PORT = '8888'
    }
    
    stages {
        stage('1️⃣ Code') {
            steps {
                echo '📝 Vérification du code...'
                sh 'ls -la'
            }
        }
        
        stage('2️⃣ Build') {
            steps {
                echo '🔨 Construction du site...'
                sh '''
                    mkdir -p output
                    cp index.html output/
                    echo "Build #${BUILD_NUMBER}" > output/version.txt
                '''
            }
        }
        
        stage('3️⃣ Docker') {
            steps {
                echo '🐳 Construction image Docker...'
                sh '''
                    docker build -t pipeline-demo:${BUILD_NUMBER} .
                    docker images | grep pipeline-demo
                '''
            }
        }
        
        stage('4️⃣ Deploy') {
            steps {
                echo '🚀 Déploiement...'
                sh '''
                    docker stop pipeline-demo 2>/dev/null || true
                    docker rm pipeline-demo 2>/dev/null || true
                    
                    docker run -d \
                        --name pipeline-demo \
                        -p ${PORT}:80 \
                        pipeline-demo:${BUILD_NUMBER}
                '''
            }
        }
        
        stage('5️⃣ Test') {
            steps {
                echo '✅ Test...'
                sh '''
                    sleep 3
                    curl -I http://localhost:${PORT} || echo "Site pas encore prêt"
                '''
            }
        }
    }
    
    post {
        success {
            echo "🎉 PIPELINE RÉUSSI ! Site: http://localhost:${PORT}"
        }
    }
}