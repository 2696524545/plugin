### 1.前端插件安装
#### 插件拷贝至gin-vue-admin/web/src/plugin/ 目录，安装软件依赖
```
    npm i monaco-editor-vue3@0.1.6 js-yaml@4.1.0  \
          vue-chartjs@4.1.1 \
          xterm@4.19.0 \
          xterm-addon-fit@0.5.0"
```
### 2.后端插件安装
#### 插件放入gin-vue-admin/server/plugin，后端插件引入
gin-vue-admin/server/initialize/plugin.go 添加
```
import  "github.com/flipped-aurora/gin-vue-admin/server/plugin/kubernetes"

PluginInit(PrivateGroup, kubernetes.CreatekubernetesPlug()) // 
kubernetes插件
```
### 3.后端插件Websocket路由配置
gin-vue-admin-plugins/server/initialize/router.go
```
初始化路由里面加入插件配置(func Routers() *gin.Engine 初始化路由方法)
kubernetesRouter := kubernetes.RouterGroupApp.WsApiRouter 
{
    systemRouter.InitBaseRouter(PublicGroup)   // 注册基础功能路由 不做鉴权
    systemRouter.InitInitRouter(PublicGroup)   // 自动初始化相关
    kubernetesRouter.InitWsRouter(PublicGroup) // WebSocket路由 （这个是新增的路由）
  }
```
### 4.后端依赖安装
```
 gin-vue-admin 目录执行:    go mod tidy        #安装插件所需依赖
```

### 5.插件协助
已购买该插件，安装出现问题，请联系QQ：2696524545 (当前插件处于促销期，两个月后会涨价，先到先得，莫失良机)

### 6.插件说明地址
> https://github.com/2696524545/plugin/blob/main/README.md

### 7.功能展示
#### 集群管理
![集群管理](https://github.com/2696524545/plugin/blob/main/clusters.png)
#### 节点管理
![节点管理](https://github.com/2696524545/plugin/blob/main/node.png)
#### 工作负载
![工作负载](https://github.com/2696524545/plugin/blob/main/workloads.png)
#### Deployment详情
![Deployment详情](https://github.com/2696524545/plugin/blob/main/DeploymentDetail.png)
#### Pod终端
![Pod终端](https://github.com/2696524545/plugin/blob/main/PodTerminal.png)
#### Pod管理
![Pod管理](https://github.com/2696524545/plugin/blob/main/Pods.png)
#### 命名空间管理
![命名空间管理](https://github.com/2696524545/plugin/blob/main/namespaces.png)
#### 网络管理
![网络管理](https://github.com/2696524545/plugin/blob/main/networks.png)
#### 配置管理
![配置管理](https://github.com/2696524545/plugin/blob/main/configs.png)
#### 存储管理
![存储管理](https://github.com/2696524545/plugin/blob/main/storages.png)

#### 访问控制
![访问控制](https://github.com/2696524545/plugin/blob/main/access.png)

