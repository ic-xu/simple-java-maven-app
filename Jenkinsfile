pipeline {
    agent {
        docker {
            image 'maven:3-alpine'
            args '-v /root/.m2:/root/.m2'
        }
    }
    stages {
        stage('Build') {
            steps {
                sh 'mvn -B -DskipTests clean package'
            }
        }
        stage('Test') { 
            steps {
                sshPublisher(publishers: [sshPublisherDesc(configName: '10.92.36.12', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '''
#!/bin/sh
#restart admin service
 
pidlist=`ps -ef|grep littlebird-admin | grep -v "grep"|awk \'{print $2}\'`
 
if [ ! -f "/home/ubuntu/target/my-app-1.0-SNAPSHOT.jar" ]; then
	echo "lmy-app jar not exists"
	exit
 
fi
 
if [ "$pidlist" = "" ];then 
	echo "no admin service starting"
	
else
	echo "my-app service pid list : $pidlist"
	echo "kinlling pid list : $pidlist"
	kill -9 $pidlist
	echo "my-app service stopped successfully!"
	sleep 2
fi
 
echo "start my-app service now"
 
nohup  java -jar /home/ubuntu/target/my-app-1.0-SNAPSHOT.jar >test.out 2>&1 &
 
echo "my-app service starting successfully!"''', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'work', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'target/*.jar')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
            }
        }
    }
}
