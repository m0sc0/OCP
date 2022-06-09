# Casos tipicos OCP :

## Síntoma:  
No entra a la consola de OCP:  

## Análisis:  
Se degrado un cluster operator.  

```
[root@bastion ~]# oc get co
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.8.36    False       False         True       18h
baremetal                                  4.8.36    True        False         False      221d
cloud-credential                           4.8.36    True        False         False      2y5d
cluster-autoscaler                         4.8.36    True        False         False      2y5d
config-operator                            4.8.36    True        False         False      628d
console                                    4.8.36    False       False         True       9h
csi-snapshot-controller                    4.8.36    True        False         False      28d
dns                                        4.8.36    True        False         False      39d
etcd                                       4.8.36    True        False         False      631d
image-registry                             4.8.36    True        False         False      39d
ingress                                    4.8.36    True        False         False      18h
insights                                   4.8.36    True        False         False      2y5d
kube-apiserver                             4.8.36    True        False         False      631d
kube-controller-manager                    4.8.36    True        False         False      631d
kube-scheduler                             4.8.36    True        False         False      631d
kube-storage-version-migrator              4.8.36    True        False         False      9h
machine-api                                4.8.36    True        False         False      2y5d
machine-approver                           4.8.36    True        False         False      628d
machine-config                             4.8.36    True        False         False      9h
marketplace                                4.8.36    True        False         False      221d
monitoring                                 4.8.36    True        False         False      39d
network                                    4.8.36    True        False         False      2y5d
node-tuning                                4.8.36    True        False         False      39d
openshift-apiserver                        4.8.36    True        False         False      20d
openshift-controller-manager               4.8.36    True        False         False      39d
openshift-samples                          4.8.36    True        False         False      39d
operator-lifecycle-manager                 4.8.36    True        False         False      2y5d
operator-lifecycle-manager-catalog         4.8.36    True        False         False      2y5d
operator-lifecycle-manager-packageserver   4.8.36    True        False         False      28d
service-ca                                 4.8.36    True        False         False      2y5d
storage                                    4.8.36    True        False         False      9h
[root@bastion ~]# 
```

```
oc get clusteroperator/console -o yaml | grep -i error
error: x509: certificate has expired or is not yet valid
```

Vemos que expiro un certificado por lo que hay que renovarlo.  

## Solución:  
https://access.redhat.com/solutions/6458661  
Certificados (oc get csr | awk '{if (NR!=1) {print $1}}' | xargs oc adm certificate approve)  
oc get secrets -n openshift-ingress | grep router-certs-default  
oc get secrets -n openshift-ingress | grep router-certs-default
oc logs router-pods -n openshift-ingress



## Síntoma:  
Se reiniciaron pods.

## Análisis:  
Vemos que el operador de machine-config esta degradado.

```
[root@bastion ~]# oc get co
NAME                                       VERSION   AVAILABLE   PROGRESSING   DEGRADED   SINCE
authentication                             4.8.36    True        False         False      18h
baremetal                                  4.8.36    True        False         False      221d
cloud-credential                           4.8.36    True        False         False      2y5d
cluster-autoscaler                         4.8.36    True        False         False      2y5d
config-operator                            4.8.36    True        False         False      628d
console                                    4.8.36    True        False         False      9h
csi-snapshot-controller                    4.8.36    True        False         False      28d
dns                                        4.8.36    True        False         False      39d
etcd                                       4.8.36    True        False         False      631d
image-registry                             4.8.36    True        False         False      39d
ingress                                    4.8.36    True        False         False      18h
insights                                   4.8.36    True        False         False      2y5d
kube-apiserver                             4.8.36    True        False         False      631d
kube-controller-manager                    4.8.36    True        False         False      631d
kube-scheduler                             4.8.36    True        False         False      631d
kube-storage-version-migrator              4.8.36    True        False         False      9h
machine-api                                4.8.36    True        False         False      2y5d
machine-approver                           4.8.36    True        False         False      628d
machine-config                             4.8.36    False       False         True       9h
marketplace                                4.8.36    True        False         False      221d
monitoring                                 4.8.36    True        False         False      39d
network                                    4.8.36    True        False         False      2y5d
node-tuning                                4.8.36    True        False         False      39d
openshift-apiserver                        4.8.36    True        False         False      20d
openshift-controller-manager               4.8.36    True        False         False      39d
openshift-samples                          4.8.36    True        False         False      39d
operator-lifecycle-manager                 4.8.36    True        False         False      2y5d
operator-lifecycle-manager-catalog         4.8.36    True        False         False      2y5d
operator-lifecycle-manager-packageserver   4.8.36    True        False         False      28d
service-ca                                 4.8.36    True        False         False      2y5d
storage                                    4.8.36    True        False         False      9h
```

```
[root@bastion ~]# oc get nodes
NAME                                  STATUS   ROLES    AGE    VERSION
infra-0.ocp-prod-acme.xxx.com.ar    Ready    infra    2y5d   v1.21.8+ee73ea2
infra-1.ocp-prod-acme.xxx.com.ar    Ready    infra    2y5d   v1.21.8+ee73ea2
infra-2.ocp-prod-acme.xxx.com.ar    Ready    infra    2y5d   v1.21.8+ee73ea2
master-0.ocp-prod-acme.xxx.com.ar   Ready    master   2y5d   v1.21.8+ee73ea2
master-1.ocp-prod-acme.xxx.com.ar   Ready    master   2y5d   v1.21.8+ee73ea2
master-2.ocp-prod-acme.xxx.com.ar   Ready    master   2y5d   v1.21.8+ee73ea2
worker-0.ocp-prod-acme.xxx.com.ar   Ready    worker   2y5d   v1.21.8+ee73ea2
worker-1.ocp-prod-acme.xxx.com.ar   Ready    worker   2y5d   v1.21.8+ee73ea2
worker-2.ocp-prod-acme.xxx.com.ar   Ready    worker   2y5d   v1.21.8+ee73ea2
worker-3.ocp-prod-acme.xxx.com.ar   NotReady worker   407d   v1.21.8+ee73ea2
```

Vemos que un nodo worker esta en not ready por lo que hubo un evento de hw/red/soft y quedo fuera del cluster provocando el reinicio del nodo y los pods migraran a otro nodo.  


## Síntoma:  
Se reiniciaron pods.

## Análisis:  
Vemos que el operador de machine-config esta ok.  
Ver en los logs del pod, ver en el yaml a ver si hay OOMKilled (ver si se queda sin memoria)
Ver en las metricas de la consola web como esta de memoria y cpu.


## Solución: 
Darle mas resources a los pods.
```
apiVersion: v1
kind: Pod
metadata:
  name: small-pod
  labels:
    app: guestbook
    tier: frontend
spec:
  containers:
  - name: php-redis
    image: gcr.io/google-samples/gb-frontend:v4
    imagePullPolicy: Always
    resources:
      limits:
        cpu: 150m
        memory: 100Mi
      requests:
        cpu: 150m
        memory: 100Mi
```

## Síntoma:  
Upgrade 

## Análisis:  
Queremos upgradear la plataforma y no actualiza.


## Solución: 
Vemos con oc get co que el mcp de machineconfigpool esta degradado
ver que no esten pausados o ver que error tiene.

```
[root@bastion ~]# oc get mcp 
NAME     CONFIG                                             UPDATED   UPDATING   DEGRADED   MACHINECOUNT   READYMACHINECOUNT   UPDATEDMACHINECOUNT   DEGRADEDMACHINECOUNT   AGE
infra    rendered-infra-75d7839757c6e5ec442ee7ec1bf97dd3    True      False      False      3              3                   3                     0                      2y5d
master   rendered-master-0c5d15e3e42366831484eeb139833637   True      False      False      3              3                   3                     0                      2y5d
worker   rendered-worker-75d7839757c6e5ec442ee7ec1bf97dd3   False     False      True       4              4                   4                     0                      2y5d
```




## Síntoma:  
Operadores dando servicio de negocio dejan de funcionar. 

## Análisis:  
Operadores en autoupdate

## Solución: 
Poner en manual la actualizacion de los operadores, probarlo en dev y luego actualizar en prod.




## Síntoma:  
Alert en OCP Console: Etcd is burning too much

## Análisis:  
La base de datos etcd esta escribiendo muchas entradas clave valor.

## Solución: 
Vemos la salud del cluster de etcd   
```
for etcd_pod in $(oc get pod -n openshift-etcd -o name -l k8s-app=etcd); do echo -e "\n##### ${etcd_pod} #####"; echo "----- Member List -----" && oc rsh -n openshift-etcd -c etcdctl ${etcd_pod} etcdctl member list -w table; echo "----- Endpoint status -----" && oc rsh -n openshift-etcd -c etcdctl ${etcd_pod} etcdctl endpoint status -w table; echo "----- Endpoint health -----" && oc rsh -n openshift-etcd -c etcdctl ${etcd_pod} etcdctl endpoint health -w table; echo "----- Alarm list -----" && oc rsh -n openshift-etcd -c etcdctl ${etcd_pod} etcdctl alarm list; done
```

Vemos logs de los pods de kube-api en busco de alguna evidencia, algun evento muy repetitivo, puede ser algun evento de red,  
algun service account tratando de logearse constantemente.



## Síntoma:  
Más casos tipicos de operacion


## Solución: 
https://github.com/gonzaloacosta/openshift-howto  









