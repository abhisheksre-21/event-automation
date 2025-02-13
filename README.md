Installed AWX using minikube
https://www.linuxtechi.com/install-ansible-awx-on-ubuntu/

Steps for setup:
1.Created Project and templete on AWX console.
2.Created ansible playbook on GIT.
3.Creted Webhook on alertmanager to trigger the script.



Issue: Faced issue while connecting to git from awx
Fix: Restarted the minikube 
To access the dashboard from Ubuntu system itself, run following command to get dashboard url,
minikube service awx-ubuntu-service --url -n ansible-awx
Access outside of your Ubuntu system then run following kubectl command:
kubectl port-forward service/awx-ubuntu-service -n ansible-awx --address 0.0.0.0 10445:80 &> /dev/null &



Issue: Connectivity issue due to different docker and awx network 
Fix: Started the alertmanager and prometheus on bridge network. 

Command to check trigger script manually: For testing 
curl -u admin:pW7FRIkSyoE6LxHaVf3iY154Wu7GhgAt      -X POST      -H "Content-Type: application/json"      -d '{
           "extra_vars": {
               "var1": "value1",
               "var2": "value2"
           }
         }'      http://192.168.49.1:10445/api/v2/job_templates/9/launch/



Command to check connectivity from alertmanager to awx:
docker exec -it alertmanager wget http://192.168.49.2:10455


Command to check alertmanager logs:
docker logs -f alertmanager


abhishek@IBLW5CD3303X8Z:~/monitoring-stack/prometheus-grafana-monitoring-stack$ minikube ip
192.168.49.2

abhishek@IBLW5CD3303X8Z:~/monitoring-stack/prometheus-grafana-monitoring-stack$ ip addr show | grep -i 198*
    inet 172.19.0.1/16 brd 172.19.255.255 scope global br-3d32d505712f
119: veth97b595b@if118: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master br-e6c51ae7cbc7 state UP group default
    inet6 fe80::a412:e2ff:feb5:b192/64 scope link
    inet 192.168.49.1/24 brd 192.168.49.255 scope global br-fc8a7415912f
    link/ether ea:12:99:55:8e:bc brd ff:ff:ff:ff:ff:ff link-netnsid 19

 kubectl port-forward service/awx-ubuntu-service -n ansible-awx --address 0.0.0.0 10445:80 &> /dev/null &

Note: Used host IP and exposed port to connect to AWX API   


To get Job ID from AWX console:
Resource->Template (We will get Job ID from URL) Example: Below url 9 is the Job ID
http://localhost:10445/#/templates/job_template/9/details


