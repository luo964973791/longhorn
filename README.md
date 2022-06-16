### helm3部署longhorn存储

```javascript
helm repo add longhorn https://charts.longhorn.io
helm repo update
helm pull longhorn/longhorn
tar xvf longhorn-x.x.x.tgz && cd longhorn-x.x.x
kubectl create ns longhorn-system

# 修改 nodeport、defaultDataPath两个字段.
vi values.yaml
service:
  ui:
    type: NodePort
    nodePort: 32000
defaultSettings:
  defaultDataPath: /data/longhorn


helm install longhorn -n longhorn-system -f ./values.yaml .
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

