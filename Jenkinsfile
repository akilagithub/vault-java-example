def projectProperties = [
    [$class: 'BuildDiscarderProperty',strategy: [$class: 'LogRotator', numToKeepStr: '3']],
]
properties(projectProperties)
pipeline {
  agent any
  
  
   

    stage('Integration Tests') {
      steps {
      sh 'curl -o vault.zip https://releases.hashicorp.com/vault/0.7.0/vault_0.7.0_linux_arm.zip ; yes | unzip vault.zip'
        withCredentials([string(credentialsId: 'role', variable: 'ROLE_ID'),string(credentialsId: 'VAULTTOKEN', variable: 'VAULT_TOKEN')]) {
        sh '''
          set -x
         
          curl https://raw.githubusercontent.com/akilagithub/vault-java-example/master/ca.crt > ca.crt
          export VAULT_CACERT=$(pwd)/ca.crt
          export VAULT_ADDR=http://52.190.2.122:8200
          
          export SECRET_ID=$(./vault write -field=secret_id -f auth/approle/role/java-example/secret-id)
          export VAULT_TOKEN=$(./vault write -field=token auth/approle/login role_id=${ROLE_ID} secret_id=${SECRET_ID})
          keytool -import -trustcacerts -file ca.crt -alias CorrarelloCA -keystore cacerts -noprompt -keypass changeit -storepass changeit
          java -D"javax.net.ssl.trustStore=./cacerts" -jar target/java-client-example-1.0-SNAPSHOT-jar-with-dependencies.jar 
        '''
        }
      }
    }
  }
  environment {
    mvnHome = 'maven-3.2.5'
  }
}
