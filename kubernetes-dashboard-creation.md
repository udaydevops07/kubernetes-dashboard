First connect with the Cluster:

If using AWS kubernetes cluster
```
aws eks --region <region> update-kubeconfig --name <cluster_name>
```
If using GCP kubernetes Cluster:
```
gcloud container clusters get-credentials <name> --zone <region> --project <project-id>
```

Step1:
```
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.6.1/aio/deploy/recommended.yaml
```
Step2:
##Create an Admin User:

```
# dashboard-adminuser.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kubernetes-dashboard
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user-binding
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kubernetes-dashboard
roleRef:
  kind: ClusterRole
  name: cluster-admin
  apiGroup: rbac.authorization.k8s.io
```
We can apply the role(admin or read only) as per your requirement. Refer [here](https://spacelift.io/blog/kubernetes-dashboard)


apply the config:

```
kubectl apply -f dashboard-adminuser.yaml or kubectl apply -f dashboard-readonlyuser.yaml
```
Step3:
Get the Access Token:
```
kubectl -n kubernetes-dashboard create token admin-user
```

##Access the Dashboard
```
kubectl proxy
```
Make changes in the URL:
replace the end url from /?authuser=0 to /api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/#/login

YOu can See the kubernetes dashboard page.

Step4:
##Exposing the dashboard:
 

create a config file to expose the service externally:

```
# dashboard-service.yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-dashboard-external
  namespace: kubernetes-dashboard
spec:
  type: LoadBalancer
  ports:
  - port: 443
    targetPort: 8443
  selector:
    k8s-app: kubernetes-dashboard
```

Apply the config:

```
kubectl apply -f dashboard-service.yaml
```
Step5:
Retrieve the External IP: 
```
kubectl -n kubernetes-dashboard get svc kubernetes-dashboard-external
```




[def]: https://spacelift.io/blog/kubernetes-dashboard
