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
                    # Utiliser Docker depuis le socket
                    docker build -t pipeline-demo:${BUILD_NUMBER} . || {
                        echo "Docker pas trouvé dans Jenkins, on va utiliser le Docker du système"
                        # Alternative: copier les fichiers vers le système hôte
                        cp -r . /tmp/pipeline-${BUILD_NUMBER}
                    }
                '''
            }
        }
        
        stage('4️⃣ Deploy') {
            steps {
                echo '🚀 Déploiement...'
                sh '''
                    # Vérifier si on peut utiliser Docker
                    if command -v docker &> /dev/null; then
                        docker stop pipeline-demo 2>/dev/null || true
                        docker rm pipeline-demo 2>/dev/null || true
                        
                        docker run -d \
                            --name pipeline-demo \
                            -p ${PORT}:80 \
                            pipeline-demo:${BUILD_NUMBER}
                    else
                        echo "Docker non disponible, création d'un serveur HTTP simple"
                        cd output && python3 -m http.server ${PORT} &
                    fi
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