📊 Prometheus + Grafana for Kubernetes Cluster Monitoring 📈

¡Bienvenido al setup de monitoreo para tu clúster de Kubernetes! Este README te guía para instalar Prometheus y Grafana, configurar el monitoreo de nodos y personalizar tu dashboard. 🚀

Instalación de Prometheus y Grafana en el clúster de Kubernetes 🌐
Añadir el repositorio Bitnami para acceder a los charts de Helm 📦
Helm es un gestor de paquetes para Kubernetes que simplifica las instalaciones. Añade el repo de Bitnami:

```bash
helm repo add bitnami https://charts.bitnami.com/bitnami
helm repo update
```
Instalar Prometheus vía chart de Helm 🎉
Despliega Prometheus para monitoreo en tiempo real:

```bash
helm install prometheus bitnami/kube-prometheus
```

Visualizar pods en tiempo real 👀
Revisa el estado de los pods:

```bash
kubectl get po -w
```

Personalizar el chart de Helm para el dashboard de Grafana 🎨
Edita un archivo values.yaml para configurar Grafana:

```bash
nano values.yaml
```

Añade la siguiente configuración:

```bash
grafana:
  service:
    type: NodePort
```

Instala el operador de Grafana:

```bash
helm install grafana -f values.yaml bitnami/grafana-operator
```

Obtener el NodePort 🔍
Lista los servicios para encontrar el puerto expuesto:

```bash
kubectl get svc 
```

Configuración de credenciales de Grafana 🔑
Crear una contraseña para el usuario predeterminado (admin) 🛡️
Intenta extraer la contraseña con este comando:

```bash
echo "Password: $(kubectl get secret grafana-admin-credentials -o jsonpath="{.data.GF_SECURITY_ADMIN_PASSWORD}" | base64 --decode)
```

⚠️ Si ocurre un error por problemas de versiones, sigue la solución alternativa.
Solución alternativa: Extraer credenciales del secret correcto 🌟
Si el comando anterior falla, inspecciona el secret:

```bash
kubectl get secret grafana-grafana-operator-grafana-admin-credentials -o yaml
```

Salida típica:

```yaml
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
Decodifica los valores:

```bash
echo "Username: $(echo "YWRtaW4=" | base64 --decode)"
echo "Password: $(echo "bVliRFNIR1lHdDk5cXc9PQ==" | base64 --decode)"
```

Resultado:

```text
Username: admin
Password: MybDSHGYt99qw==
```

Cambia la contraseña una vez dentro 🔧
Accede a Grafana y actualiza la contraseña predeterminada por seguridad.
Configurar la fuente de datos en Grafana 📊

Añade tu primera fuente de datos y selecciona Prometheus.
En la URL, usa el servicio de Prometheus. Lista los servicios:

```bash
kubectl get svc
```

Salida ejemplo:

```text
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
```
Configura la URL como:

```text
url: http://prometheus-kube-prometheus-prometheus.default.svc.cluster.local:9090
```
Haz clic en Save and Test para confirmar. ✅
