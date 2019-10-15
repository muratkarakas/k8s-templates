kubectl port-forward svc/netdata 6767:19999


cat <<EOF | kubectl create -f -
apiVersion: v1
kind: ServiceAccount
metadata:
  name: tiller
  namespace: kube-system
---
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: tiller
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
  - kind: ServiceAccount
    name: tiller
    namespace: kube-system
EOF

helm init --service-account tiller


helm install --name customer-service  ./helm

helm upgrade  customer-service  ./helm

helm delete customer-service --purge

helm install --name customer-service  ./helm --namespace cna

helm install stable/metrics-server



helm repo add coreos https://s3-eu-west-1.amazonaws.com/coreos-charts/stable/
helm install coreos/prometheus-operator --name prometheus-operator --namespace monitoring
helm install coreos/kube-prometheus --name kube-prometheus --set global.rbacEnable=true --namespace monitoring


kubectl --namespace monitoring port-forward svc/kube-prometheus-grafana 3000:80


kubectl --namespace monitoring port-forward svc/grafana 3000

ab -c 10 -n 500  http://localhost:32080/hello

kubectl patch deployment web -p \
  "{\"spec\":{\"template\":{\"metadata\":{\"labels\":{\"date\":\"`date +'%s'`\"}}}}}"


helm upgrade --install kubewatch stable/kubewatch --values=kubecwatch.yaml


kubectl get deploy -o yaml | linkerd inject - | kubectl apply -f -


or

apiVersion: v1
kind: Namespace
metadata:
  name: sample-inject-enabled-ns
  annotations:
    linkerd.io/inject: enabled



kubectl run -i --tty busybox --image=busybox --restart=Never  -- sh   
kubectl run -i --tty busybox --image=busybox --restart=Never  -n=secondary  -- sh   
