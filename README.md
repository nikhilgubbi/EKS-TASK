# Eks-Task 
 Hi guys Today we will see how Eks work and how we can launch our own kubernates cluster in just a few clicks on aws.
* Prequisite aws account,basic knowledge of how kubernates work,basics of aws.
* So first of all create IAM role on aws with admin access and configure aws cli. We are going to launch all the things using aws cli. We can launch with the help of gui also. But gui is not accesible all the time so its good to practice from cli.
* Once IAM is created and aws cli is configured download the kubectl using this link (https://kubernetes.io/docs/tasks/tools/install-kubectl/) and eksctl(https://docs.aws.amazon.com/eks/latest/userguide/getting-started-eksctl.html).
Once this is downloaded make sure to add them into the enviroment variable so that it can be accesed from anywhere.Once its done we are all set to launch our own Kubernates cluster.
* First clone this repo files and open cluster.yaml file and there you can change things according to your need such as cluster name and there os, one term called node group in which we have to give information of type of node we want. Here I have used t2.micro beacause they are free.
**Remember that eks is not a free service. We have to pay something. Check pricing before doing anything**  once the cluster file is ready save it and close it.
* run the following command ("eksctl create cluster -f filename") It will take around 10 to 15 min.
* Now we have to make or upgrade  kube-coonfig file. For that just run the followong command ("aws eks upadte-kubeconfig --name clustername").
* Once this is done now, we are all set to to use k8s as it is we use in our own cluster or in minikube today. In k8s we are going to launch wordpress and mysql  and we have designed this in such a way that database will not be exposed to anyone means it will use internal ip and wordpress  works as front end so that we will use load balancer service and here we use ELB service of AWS for load balancing and for storage by deafult we use EBS. But we can launch on EFS so that all node save the data to one place.
* For running this setup we have to run the following command ("kubectl create -k .) Here we are saying kubernates to run kustamization.yml file. Its one file which contain secrets for database and here we set which file to run and when we never want. First our Wordpress is created and then Mysql. What we want is first mysql db is created and then Wordpress deploy on it so kustamization file will take care of it.

 * Once pod is deployed we can see service by(" kubectl get svc") and  take domain of Wordpress and can see  your Wordpress site. 
 * Here we can  also use Helm to install helm run following cmnd:-
 * - ("helm repo add stable https://kubernetes-charts.storage.googleapis.com/
      
      helm repo list
      
      helm repo update)
* Once this is done we have to set tiller. To set tiller run the followoing cmnd :-
* -("kubectl -n kube-system create serviceaccount tiller 

kubectl create clusterrolebinding tiller --clusterrole cluster-admin --serviceaccount=kube-system:tiller 

helm init --service-account tiller

kubectl get pods --namespace kube-system

helm init --service-account tiller --upgrade")

* In my previous article i have shown how to launch promethesus and grafana  from yaml file if not see chek this out **https://www.linkedin.com/pulse/integrating-prometheus-grafana-using-kubernetes-nikhil-gr**.
* Today we are going to use helm package to launch prometheus and grafana. To do so run the following commands.

 * For Prometheus run the following command (" kubectl create namespace prometheus

helm install  stable/prometheus     --namespace prometheus     --set alertmanager.persistentVolume.storageClass="gp2"     --set server.persistentVolume.storageClass="gp2"

kubectl get svc -n prometheus

kubectl -n prometheus  port-forward svc/whimsical-markhor-prometheus-server  8888:80").

* For grafana  run following command ("kubectl create namespace grafana

helm install stable/grafana  --namespace grafana     --set persistence.storageClassName="gp2" --set adminPassword='GrafanaAdm!n'    --set datasources."datasources\.yaml".apiVersion=1     --set datasources."datasources\.yaml".datasources[0].name=Prometheus   --set datasources."datasources\.yaml".datasources[0].type=prometheus    --set datasources."datasources\.yaml".datasources[0].url=http://prometheus-server.prometheus.svc.cluster.local   --set datasources."datasources\.yaml".datasources[0].access=proxy     --set datasources."datasources\.yaml".datasources[0].isDefault=true  --set service.type=LoadBalancer

kubectl get  secret  fair-numbat-grafana   --namespace  grafana  -o yaml")
* This is how you can launch launch prometheus and grafana using helm and you can also lauch jenkins service on k8s same as i discuss this in my article **https://www.linkedin.com/pulse/jenkins-kubernetes-nikhil-gr**.
* Now we have seen there is one manual thing that we have to do is setting node groups. We need some time, don't have much knowlege which node is required when or sometime we need more from that we plan so for that there is one service called fargat cluster and it will handle all the things by itslef, even the node also is controlled by this service. Only thing we have to do is to create fargat cluster and we are done. Rest other things will be taken care by  fargat service ( this service is sub service of ECS in aws).
* To launch fargat cluster   open fcluster .yml file and there we can set name of the cluster and namapspace we want to use and once done save it and close it. Once this is done we are ready to laucnh fargat cluster run the following command("eksctl create cluster -f filename). It will take time to launch the cluster we can check whether the cluter is created or not by ("eksctl get cluster --region you region")
* Once fargat cluster is created now we have to update kube config for that run  the following command (" aws eks --region your region update-kubeconfig  --name cluster name "). After this we can use kubectl get nodes to  check nodes launched by fargat cluster interanlly.



