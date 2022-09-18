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

初始化路由里面加入插件配置(func Routers() *gin.Engine 初始化路由方法)
```
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

### 4.功能展示

![集群管理](https://github.com/2696524545/plugin/blob/main/clusters.png)
