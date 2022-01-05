# Welcome to the Certified-Kubernetes-Administrator-CKA-2022-Mock-Exam !
## https://aminshateri.com/
## https://www.linkedin.com/in/amin-shateri/

_1. Create a new ClusterRole named **deployment-clusterrole**,which only allows to **create** the following resource types:_
_**Deployment**,_
_**StatefulSet**,_
_**DaemonSet**_

_Create a new ServiceAccount named **cicd-token** in the existing namespace **app-team1**. Bind the new ClusterRole **deployment-clusterrole** to the new ServiceAccount **cicd-token** , limit to the namespace **app-team1**_

# Answer
first, you should create a service account named **cicd-token** in a **app-team1** namespace

`# kubectl create serviceaccount cicd-token --namespace=app-team1`

second, create a cluster role named deployment-cluster role with **deployments**,**statefulsets**, and **daemonsets** resources and **create** verb.

`# kubectl create clusterrole deployment-clusterrole --verb=create --resource=deployments,statefulsets,daemonsets`

finally, you can bind the clusterrole to the service account

`# kubectl create rolebinding deployment-clusterrole --clusterrole=deployment-clusterrole --serviceaccount=app-team1:cicd-token --namespace=app-team1`
***

_2. Set the node labelled with name=**ek8s-node-1** as unavailable and reschedule all the pods running on it._

# Answer
`# kubectl drain ek8s-node-1 --delete-local-data --ignore-daemonsets --force`
***


_3. Given an existing Kubernetes cluster running version **1.21.1**, upgrade all of the Kubernetes control plan and node components on the master node only to version **1.21.2**. You are also expected to upgrade kubelet and kubectl on the **master node**._

# Answer
* Upgrade kubeadm:

`# apt-mark unhold kubeadm && apt-get update && apt-get install -y kubeadm=1.21.2-00 && apt-mark hold kubeadm`

* Drain the node 

`# kubectl drain master --ignore-daemonsets`

* Choose a version to upgrade to, and run the appropriate command. For example:

`# kubeadm upgrade apply v1.21.2`

* Upgrade kubelet and kubectl 

`# apt-mark unhold kubelet kubectl && apt-get install -y kubelet=1.21.2-00 kubectl=1.21.2-00 && apt-mark hold kubelet kubectl`

* Restart the kubelet:

`# systemctl daemon-reload && systemctl restart kubelet && kubectl uncordon master`
***


_4. Create a snapshot of the existing etcd instance running at **https://127.0.0.1:2379** saving the snapshot to **/srv/data/etcd-snapshot.db**_

_Next, restore an existing, previous snameshot located at **/var/lib/backup/etcd-snapshot-previous.db**._

_The following TLS certificates/key are supplied for connecting to the server with etcdctl:_

_CA certificate: **/opt/KUIN00601/ca.crt** Client certificate: **/opt/KUIN00601/etcd-client.crt** Clientkey:**/opt/KUIN00601/etcd-client.key**_

# Answer
* backup from etcd:

`# ETCDCTL_API=3 etcdctl --endpoints="https://127.0.0.1:2379" --cacert=/opt/KUIN000601/ca.crt --cert=/opt/KUIN000601/etcd-client.crt --key=/opt/KUIN000601/etcd-client.key snapshot save /etc/data/etcd-snapshot.db`

* restore from etcd:

`# ETCDCTL_API=3 etcdctl --endpoints="https://127.0.0.1:2379" --cacert=/opt/KUIN000601/ca.crt --cert=/opt/KUIN000601/etcd-client.crt --key=/opt/KUIN000601/etcd-client.key snapshot restore /var/lib/backup/etcd-snapshot-previoys.db`
***


_5. Create a new **NetworkPolicy** name **allow-port-from-namespace** that allows Pods in the existing namespace **echo** to connect to port **9000** of other Pods in the same namespace, Ensure that the new NetworkPolicy:_

_* does not allow access to Pods not listening on port 9000_

_* does not allow access from Pods not in namespace internal_

# Answer
* First, you should search "NetworkPolicy" in the kubernetes.io document and then copy the first NetworkPolicy YAML file and then delete unused lines, finally, the below YAML file will remain:

`# vim networkpol.yaml`
```YAML
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: all-port-from-namespace        #This line is changed
  namespace: echo        #This line should be changed
spec:
  podSelector:
    matchLabels: {}
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          name: internal        #This line is changed 
    - podSelector: {}
    ports:
    - port: 9000        #This line is changed
```

* Then you should create network policy with

`# kubectl create -f networkpol.yaml`
***


_6. Reconfigure the existing deployment **front-end** and add a port specification named http exposing port **80/tcp** of the existing container **nginx**_

_Create a new service named **front-end-svc** exposing the container port http._

_Configure the new service to also expose the individual Pods via a **NodePort** on the nodes on which they are scheduled_

# Answer
`# kubectl expose deployment front-end --name=front-end-svc --port=80 --target-port=80 --type=NodePort`
***


_7. Create a new nginx **Ingress** resource as follows:_

* _Name: **pong**_

* _Namespace: **ing-internal**_

* _Exposing service hi on path **/hi** using service port **5678** The availability of service hi can be checked using the following command, which should return hi:_

_Answer: curl -kL <INTERNAL_IP>/hi_

# Answer
* First, you should search "Ingress" in the kubernetes.io document and then copy the "A minimal Ingress resource example:" and then change the required line according to the question Ingress.yaml:
```YAML
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: pong                # this line should be change
  namespace: ing-internal                # this line should be change
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - http:
      paths:
      - path: /hi                # this line should be change
        pathType: Prefix
        backend:
          service:
            name: hi                # this line should be change
            port:
              number: 5678
```
* Second you have to create Ingress with:

`kubectl create -f Ingress.yaml`
***


_8. Scale the deployment **loadbalancer** to **6** pods._

# Answer
`# kubectl scale deploy loadbalancer --replicas=6`
***


_9. Schedule a pod as follow:_

* _Name: **nginx-kusc00401**_
* _Image: **nginx**_
* _Node selector: **disk=spinning**_

# Answer
* First, you should search "nodeselector" in the kubernetes.io document and then copy or download the "https://k8s.io/examples/pods/pod-nginx.yaml" and then change the required line according to the question:

```YAML
apiVersion: v1
kind: Pod
metadata:
  name: nginx-kusc00401                # this line should be change
  labels:
    role: nginx-kusc00401              # this line should be change
spec:
  nodeSelector:
    disk: spinning                     # this line should be change
  containers:
    - name: nginx                      # this line should be change
      image: nginx                     # this line should be change
```
* Second you have to create pod with:

`# kubectl create -f podnodeselect.yaml`
***


_10. Check to see how many nodes are **ready** (not including nodes **tainted** **NoSchedule**) and write the number to **/opt/kubernetes/nodenum**_

# Answer
`# kubectl describe nodes |grep Taint |grep -v NoSchedule |wc -l >/opt/kubernetes/nodenum`
***


_11. Create a pod named kucc4 with a single container for each of the following images running inside:_
* _Image: **redis**_
* _Image: **consul**_

# Answer
* First, you can create a simple pod with kubectl command:
`kubectl run kucc4 --image=redis –dry-run=client -o yaml >multipod.yaml`

* Second, you should edit multipod.yaml and then edit appropriate lines:
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: kucc4
spec:
  containers:
  - image: redis
    name: redis
  - image: consul
    name: consul
```
* third you can create pod with:
`kubectl create -f multipod.yaml`
***


_12. Creae a persistent volume with name **app-config** of capacity **1Gi** and access mode **ReadWriteOnce**. The type of volume is **hostPath** and its location is **/srv/app-config**_

# Answer
* First, you should search "Configure a Pod to Use a PersistentVolume" in kubernetes.io Documentation and then copy the "https://k8s.io/examples/pods/storage/pv-volume.yaml" and then edit the yaml file as well
```YAML
apiVersion: v1
kind: PersistentVolume
metadata:
  name: app-config                # edit this line
  labels:
    type: local
spec:
  capacity:
    storage: 1Gi                  # edit this line
  accessModes:
    - ReadWriteOnce               # edit this line
  hostPath:
    path: "/srv/app-config"       # edit this line
```
* Second you should create pv with appropriate command:

`# kubectl create -f pv-volume.yaml`
***


_13. Create a new PVC:_

* _Name: **pv-volume**_
* _Class: **csi-hostpath-sc**_
* _Capacity: **10Mi** Create a new Pod which mounts the PVC as a volume:_
* _Name: **web-server**_
* _Image: nginx_
* _Mount path: **/usr/share/nginx/html** Configure the new Pod to have **ReadWriteOnce** access on the volume. Finally, using **kubectl edit** or **kubectl path** expand the PVC to a capacity **70Mi** and record that change._

# Answer
* first, you can check storageclass with "kubectl get sc" and you will see "csi-hostpath-sc" at the output, Then you can create a pvc with sample pvc  "https://k8s.io/examples/pods/storage/pv-claim.yaml" in kubernetes.io documentation, finally you can edit the sample file as well as below:
```YAML
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: pv-volume                         # this line should be edit
spec:
  accessModes:
    - ReadWriteOnce                       # this line should be edit
  volumeMode: Filesystem
  resources:
    requests:
      storage: 10Mi                       # this line should be edit
  storageClassName: csi-hostpath-sc       # this line should be add
```
* second, you should create a pod to use PVC as shown below:
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: web-server
spec:
  containers:
    - name: nginx
      image: nginx
      volumeMounts:
      - mountPath: "/usr/share/nginx/html"
        name: pv-volume
  volumes:
    - name: pv-volume
      persistentVolumeClaim:
        claimName: pv-volume
```
* Finally, you can edit pv-volume PVC with the "`kubectl edit pvc pv-volume –save-config`" command, after that you can change capacity from "10 Mi" to "70 Mi".you can test the change with the "`kubectl get pvc`" command.
***


_14. Monitor the logs of pod **loadbalancer** and:_

* _Extract log lines corrsponding to **Error unable-to-access-website**_
* _Write them to **/opt/KUTR00101/loadbalancer**_
# Answer
`kubectl logs loadbalancer |grep Error |grep unable-to-access-website > /opt/KUTR00101/loadbalancer`
***


_15. Without changing its existing containers, an existing Pod needs to be integrated into Kubernetes’s built-in logging architecture(e.g. kubectl logs). Adding a streaming sidecar container is a good and common way to accomplish this requirement._

_Add a **busybox** sidecar container to the existing Pod **legacy-app**. The new sidecar container has to run the following command: **/bin/sh -c tail -n+1 -f /var/log/legac-appp.log**_

_Use a volume mount named logs to make the file **/var/log/legacy-app.log** available to the sidecar container._

* _Don’t modify the existing container._

* _Don’t modify the path of the log file, both containers must access it at **/var/log/legacy-app.log**._

# Answer
First you should get pod with "`kubectl get pod legacy-app -o yaml >legacy-app.yaml`" then you should edit yaml file as shown below and finally apply the change by "`kubectl apply -f legacy-app.yaml`"
```YAML
apiVersion: v1
kind: Pod
metadata:
  name: podname
spec:
  containers:
  - name: count
    image: busybox
    args:
    - /bin/sh
    - -c
    - >
      i=0;
      while true;
      do
        echo "$(date) INFO $i" >> /var/log/legacy-ap.log;
        i=$((i+1));
        sleep 1;
      done
    volumeMounts:
    - name: logs
      mountPath: /var/log
  - name: count-log-1
    image: busybox
    args: [/bin/sh, -c, 'tail -n+1 -f /var/log/legacy-ap.log']
    volumeMounts:
    - name: logs
      mountPath: /var/log
  volumes:
  - name: logs
    emptyDir: {}
```
***


_16. From the pod label **name=app**,find pods running **high CPU** workloads and write the **name of the pod consuming most CPU** to the fule **/opt/KUT00401/KUT0001.txt** (which already exists)._

# Answer
`# kubectl top pod --sort-by=cpu --selector name=app |head -2 |tail -1 |cut-d' ' -f1 >/opt/KUT00401/KUT0001.txt`
***


_17. A Kubernetes worker node, named **wk8s-node-0** is in state **NotReady**. Investigate why this is the case, and perform any appropriate steps to bring the node to a Ready state, ensuring that any changes are made **permanent**._

# Answer
* First, you should go to the wk8s-node-0 node with "`ssh wk8s-node-0`" and then switch to root user with "`sudo -i`", then you can run "`kubectl get node`" and after that, you can test the kubelet service with "`systemctl status kubelet.service`" finally you will find that kubelet is stoped and all you need is start and enable kubelet as shown below:

`# systemctl start kubelet.service`

`# systemctl enable kubelet.service`
