### helm3部署longhorn存储

```javascript
#所有服务器都yum install nfs-utils iscsi-initiator-utils -y
yum install nfs-utils iscsi-initiator-utils -y
systemctl enable iscsid
systemctl start iscsid
helm repo add longhorn https://charts.longhorn.io
helm repo update


#在k8smaster服务器上部署
helm install longhorn \
  --namespace longhorn-system \
  --create-namespace \
  --set defaultSettings.defaultDataPath="/var/lib/longhorn/" \
  --set defaultSettings.defaultReplicaCount=3 \
  --set service.ui.type=NodePort \
  --set service.ui.nodePort=30890 \
  longhorn/longhorn
```

### 创建pvc测试.

```javascript
[root@localhost ~]# kubectl get storageclass
NAME                 PROVISIONER          RECLAIMPOLICY   VOLUMEBINDINGMODE   ALLOWVOLUMEEXPANSION   AGE
longhorn (default)   driver.longhorn.io   Delete          Immediate           true                   5m51s



[root@node1 ~]# cat pvc.yaml 
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: longhorn-volv-pvc
  namespace: default
spec:
  accessModes:
    - ReadWriteMany
  storageClassName: longhorn
  resources:
    requests:
      storage: 2Gi
 
 
 kubectl apply -f pvc.yaml
```

