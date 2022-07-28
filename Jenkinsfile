node {
    stage("Git Clone"){
        git credentialsId: 'GITHUB', url: 'https://github.com/tungnguyen1907/kubernetes-full-stack-example.git'
    }
    stage("build api"){
        sh 'docker version'
        
        dir ("spring-boot-student-app-api"){
            sh 'mvn install'
        }
    }
    stage("build client"){
        dir ("react-student-management-web-app"){
            sh 'docker build -t tungnguyenhcl/student-app-client .'
        }
    }
    withCredentials([string(credentialsId: 'DOCKERHUB', variable: 'PASSWORD')]) {
        sh 'docker login -u tungnguyenhcl -p $PASSWORD'
    }
    stage("Push Image to Docker Hub"){
        sh 'docker push tungnguyenhcl/student-app-client'
        dir ("spring-boot-student-app-api"){
            sh 'mvn dockerfile:push'
            }
        
    }
    stage("istio"){
	    sh 'helm repo add istio https://istio-release.storage.googleapis.com/charts'
	    sh 'helm repo update'
	    //sh 'kubectl create namespace istio-system'
	    sh 'helm upgrade istio-base istio/base -n istio-system --install'   
	    sh 'helm upgrade istiod istio/istiod -n istio-system --wait --install'
    }
    stage("istio-ingress"){
	    //sh 'kubectl create namespace istio-ingress'
	    //sh 'kubectl label namespace default istio-injection=enabled'
	
            sh 'helm upgrade istio-ingress istio/gateway -f ipallow.yaml --install'
	
    }
    stage("deploy app"){
	    sh 'helm upgrade done helmchart/ --install' 
	
	    sh 'kubectl apply -f gateway.yaml'
	
    }
    stage("prometheus"){
	    sh 'helm repo add prometheus-community https://prometheus-community.github.io/helm-charts'
	    sh 'helm upgrade prometheus prometheus-community/prometheus --install'
	    //sh 'kubectl expose service prometheus-server --type=NodePort --target-port=9090 --name=prometheus-server-np'
     }
    stage ("grafana"){
	    sh 'helm repo add bitnami https://charts.bitnami.com/bitnami'
	    sh 'helm upgrade grafana bitnami/grafana --install'
	    //sh 'kubectl expose service grafana --type=NodePort --target-port=3000 --name=grafana-np'
    }
}
