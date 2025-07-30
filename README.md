# Prometheus + Grafana for Kubernetes Cluster Monitoring
Instalación de prometheus y grafana en el clúster de kubernetes para el monitoreo de los nodos.

# Adding bitnami repo for access to prometheus helm charts
```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
```
# helm is an packet manager for kubernetes
```bash
helm repo update
```
# Installing prometheus via helm chart
```bash
helm install prometheus bitnami/kube-prometheus
```
# For pod visualitation in real time
```bash
k get po -w
```
# Customize the helm chart for grafana dashboard
```bash
nano values.yaml
```
```bash
grafana:
  service:
    type: NodePort
```
```bash
helm install grafana -f values.yaml bitnami/grafana-operator
```
# Get the nodeport
```bash
k get svc 
```
# Create a password for the grafana defauld username(admin)
```bash
echo "Password: $(kubectl get secret grafana-admin-credentials -o jsonpath="{.data.GF_SECURITY_ADMIN_PASSWORD}" | base64 --decode)
```
# Puede darse el caso de que por problemas de versiones el comando anterior de error
# La solucion que le encontre:

```bash
kubectl get secret grafana-grafana-operator-grafana-admin-credentials -o yaml
```
# Te muestra lo siguiente, de aqui nos sirve  GF_SECURITY_ADMIN_PASSWORD: bVliRFNIR1lHdDk5cXc9PQ== y GF_SECURITY_ADMIN_USER: YWRtaW4=
```bash
apiVersion: v1
data:
  GF_SECURITY_ADMIN_PASSWORD: bVliRFNIR1lHdDk5cXc9PQ==
  GF_SECURITY_ADMIN_USER: YWRtaW4=
kind: Secret
metadata:
  creationTimestamp: "2025-07-30T18:27:57Z"
  labels:
    app.kubernetes.io/instance: grafana
    app.kubernetes.io/managed-by: grafana-operator
    app.kubernetes.io/name: grafana-operator
    app.kubernetes.io/version: 5.18.0
    helm.sh/chart: grafana-operator-4.9.25
  name: grafana-grafana-operator-grafana-admin-credentials
  namespace: default
  ownerReferences:
  - apiVersion: grafana.integreatly.org/v1beta1
    blockOwnerDeletion: true
    controller: true
    kind: Grafana
    name: grafana-grafana-operator-grafana
    uid: b66c712a-1c60-442f-9132-5292ecf5817b
  resourceVersion: "27758"
  uid: 4753d3e4-9d88-432b-a6db-e9cb5174c7a6
type: Opaque
```
# Entonces procedemos a desencriptar los valores GF_SECURITY_ADMIN_PASSWORD: bVliRFNIR1lHdDk5cXc9PQ== y GF_SECURITY_ADMIN_USER: YWRtaW4=
```bash
echo "Username: $(echo "YWRtaW4=" | base64 --decode)"
Username: admin
 echo "Password: $(echo "bVliRFNIR1lHdDk5cXc9PQ==" | base64 --decode)"
Password: mYbDSHGYGt99qw==
```
# Una vez adentro cambiar password para el deseado
# Add your first data source y seleccionamos Prometheus
# En URL tenemos que poner el resultado de este codigo y buscamos el que tenga expuesto el puerto 9090

```bash
kubectl get svc
NAME                                           TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)                      AGE
alertmanager-operated                          ClusterIP   None            <none>        9093/TCP,9094/TCP,9094/UDP   34m
grafana-grafana-operator-grafana-alerting      ClusterIP   None            <none>        9094/TCP                     26m
grafana-grafana-operator-grafana-service       NodePort    xx.xx.xx.xx     <none>        3000:30192/TCP               26m
kubernetes                                     ClusterIP   xx.xx.xx.xx     <none>        443/TCP                      5h31m
prometheus-kube-prometheus-alertmanager        ClusterIP   xx.xx.xx.xx     <none>        9093/TCP                     35m
prometheus-kube-prometheus-blackbox-exporter   ClusterIP   xx.xx.xx.xx     <none>        19115/TCP                    35m
prometheus-kube-prometheus-operator            ClusterIP   xx.xx.xx.xx     <none>        8080/TCP                     35m
prometheus-kube-prometheus-prometheus          ClusterIP   xx.xx.xx.xx     <none>        9090/TCP                     35m
prometheus-kube-state-metrics                  ClusterIP   xx.xx.xx.xx     <none>        8080/TCP                     35m
prometheus-node-exporter                       ClusterIP   xx.xx.xx.xx     <none>        9100/TCP                     35m
prometheus-operated                            ClusterIP   None            <none>        9090/TCP                     34m

url: http://prometheus-kube-prometheus-prometheus.default.svc.cluster.local:9090
```
# Save and Test
