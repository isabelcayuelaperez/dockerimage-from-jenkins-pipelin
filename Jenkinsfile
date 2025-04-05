def produccion = [:]
            produccion.name = 'curso'
            produccion.host = '172.17.0.3'
            produccion.user = 'root'
            produccion.password = 'root'
            produccion.allowAnyHosts = true
pipeline {
    agent any
    stages {
        stage('Build') {
              steps {
                echo 'Fingimos que compilamos el proyecto'
                writeFile file: 'codigo/webapp/target/webapp.war', text: 'fake WAR content'
              }
            }
        stage('Test') {
            steps {
                sh "echo 'Realización de algunos Test'"    
            }
            
        }
        stage('Container') {
            steps {
                sh "echo 'Creo el contenedor'"
                dir('contenedor') {
                    withCredentials([usernamePassword(credentialsId: 'gitprueba', passwordVariable: 'password', usernameVariable: 'usuario')]) {
                        git 'https://github.com/isabelcayuelaperez/dockerimage-from-jenkins-pipelin'
                        sh('''
                            cp ../codigo/webapp/target/webapp.war .
                            git add .
                            git config --global user.email 'testif@iesalixar.org'
                            git config --global user.name  'testif@iesalixar.org'
                            git commit -m 'Desde Jenkinsfile'
                            git config --local credential.helper "!f() { echo username=\\$usuario; echo password=\\$password; }; f"
                            git push origin master
                        ''')
                    }
                }
                
            }
        }
        stage('Deploy') {
            steps {
                sh "echo 'Desplegando'"    
                sshPut remote: produccion, from: 'codigo/webapp/target/webapp.war' , into: '/usr/local/tomcat/webapps/'    
            }
            
        }
        
    }
}
