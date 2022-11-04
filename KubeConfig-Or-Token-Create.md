# 如何手动生成 Kubernetes Config 文件

```
生成集群管理权限的 config 证书
如果需要对客户端做细粒度的权限控制，可以通过以下方式生成 config 文件。
```

### 1.创建 ~/.kube/config 文件，并修改文件内容
```
apiVersion: v1
clusters:
- cluster:
    # needed if you get error "Unable to connect to the server: x509: certificate signed by unknown authority"
    insecure-skip-tls-verify: true
    server: https://YOUR_API_SERVER_PUBLIC_IP:6443
  name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: kubernetes-admin
  name: kubernetes-admin@kubernetes
current-context: kubernetes-admin@kubernetes
kind: Config
preferences: {}
users:
- name: kubernetes-admin
  user:
    client-certificate-data: LS0tLS1CRUdJTi... (base64 /etc/kubernetes/ssl/node-node1.pem)
    client-key-data: LS0tLS1CRUdJTiBS.. (base64 /etc/kubernetes/ssl/node-node1-key.pem)
```

### 2.替换公钥私钥
```
其中 client-certificate-data 来源于：
cat /etc/kubernetes/ssl/node-node1.pem | base64 -w 0

client-key-data 来源于以下命令的输出：
cat /etc/kubernetes/ssl/node-node1-key.pem | base64 -w 0
```

### 3.验证证书：

$ kubectl get nodes
NAME      STATUS    AGE       VERSION
my-kube   Ready     2h        v1.6.7+coreos.0

# 如何创建 Kubernetes Token凭据
### 集群版本小于1.24
```
注意： Kubernetes < 1.24 创建的
```
#### 1.创建用户
```
# mkdir -p  role
# cd  /role
在kube-system下创建admin-user
vi CreateServiceAccount.yaml
apiVersion: v1
kind: ServiceAccount
metadata:
  name: admin-user
  namespace: kube-system
```
#### 2.用户赋权
```
# vi RoleBinding.yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: admin-user
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- kind: ServiceAccount
  name: admin-user
  namespace: kube-system
```  
#### 3.创建资源
```
# kubectl create -f CreateServiceAccount.yaml
# kubectl create -f RoleBinding.yaml
```

#### 4.获取token
```
# Token=$(kubectl describe secret $(kubectl get secret -n kube-system | grep ^admin-user | awk '{print $1}') -n kube-system | grep -E '^token'| awk '{print $2}')
# echo $Token
```



### 集群版本 >= 1.24
```
Kubernetes >= 1.24 基于安全方面的考虑Secret API将不会为ServiceAccount自动创建Secret对象存放Token信息，
需要使用TokenRequest API来获取ServiceAccount的Token。该Token具备过期时间，更加安全。
```
#### 1.角色授权
```
kubectl create serviceaccount admin-user
kubectl create clusterrolebinding admin-user-binding \
  --clusterrole cluster-admin \
  --serviceaccount default:admin-user
 ```
#### 2.创建Secret
 ```
 cat <<EOF | kubectl create -f -
apiVersion: v1
kind: Secret
metadata:
  name: admin-user-token
  annotations:
    kubernetes.io/service-account.name: admin-user
type: kubernetes.io/service-account-token
EOF
 ```
 #### 3.打印Token
 ```
 SECRET_NAME="admin-user-token"
TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
echo $TOKEN
 ```
