### 升级内容
```
   1.集成Kubevela应用管理， 工作流审批，多集群信息查看，多集群Pod查看，终端，终端日志
```

### 容器管理插件能力
```
   1. K8S原生资源管理，多集群管理，提供表单资源管理，YAML方式管理，终端管理，终端审计等能力
   2. Gin-Vue-Admin 用户与Kubernetes RBAC打通，提供个人凭据申请，集群用户管理，集群用户授权，集群权限管理能力
   3. 阿里云开源控制器Openkruise管理能力
   4. 集成Prometheus 监控能力
   5. 集成KubeBlocks管理能力
   6. 集成Kubevela应用管理能力

方便运维对Kubernetes集群资源的细粒度授权，方便开发管理Kubernetes内的应用对其进行故障排查，提供友好的操作页面降低使用复杂性。


```
### 1.前端插件安装
#### 插件拷贝至gin-vue-admin/web/src/plugin/ 目录，安装软件依赖
```
    npm i monaco-editor-vue3@0.1.6 js-yaml@4.1.0  \
          vue-chartjs@4.1.1 \
          xterm@4.19.0 \
          xterm-addon-fit@0.5.0" \
          js-base64@^3.7.3 \
          asciinema-player@^3.6.1 \
          vue3-tree-org@^4.2.2 \
          monaco-editor@^0.48.0
```
####  vue3TreeOrg 插件引用全局main.js
文件路径:  src/main.js

```
import 'element-plus/es/components/message/style/css'
import 'element-plus/es/components/loading/style/css'
import 'element-plus/es/components/notification/style/css'
import 'element-plus/es/components/message-box/style/css'
import './style/element_visiable.scss'
import { createApp } from 'vue'
// 引入gin-vue-admin前端初始化相关内容
import './core/gin-vue-admin'
// 引入封装的router
import router from '@/router/index'
import '@/permission'
import run from '@/core/gin-vue-admin.js'
import auth from '@/directive/auth'
import { store } from '@/pinia'
import App from './App.vue'
import { initDom } from './utils/positionToCode'
import vue3TreeOrg from 'vue3-tree-org'          # 这里是引入的第三方包
import 'vue3-tree-org/lib/vue3-tree-org.css'     # 这里是引入的第三方包

initDom()
/**
 * @description 导入加载进度条，防止首屏加载时间过长，用户等待
 *
 * */
import Nprogress from 'nprogress'
import 'nprogress/nprogress.css'
Nprogress.configure({ showSpinner: false, ease: 'ease', speed: 500 })
Nprogress.start()

/**
 * 无需在这块结束，会在路由中间件中结束此块内容
 * */

const app = createApp(App)
app.config.productionTip = false

app
  .use(run)
  .use(vue3TreeOrg)   # 这里是引入的第三方包
  .use(store)
  .use(auth)
  .use(router)
  .mount('#app')

export default app

```
### 2.后端插件安装
#### 插件放入gin-vue-admin/server/plugin，后端插件引入
gin-vue-admin/server/initialize/plugin.go 添加
```
import  "github.com/flipped-aurora/gin-vue-admin/server/plugin/kubernetes"

PluginInit(PrivateGroup, kubernetes.CreateKubernetesPlug()) // 
kubernetes插件
```
### 3.后端插件Websocket路由配置
gin-vue-admin/server/initialize/router.go
```
导入路由: 
   kubernetes "github.com/flipped-aurora/gin-vue-admin/server/plugin/kubernetes/router"
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
已购买该插件，安装出现问题，请联系Gin-Vue-Admin获取插件作者联系方式 (当前插件处于促销期，两个月后会涨价，先到先得，莫失良机)

### 6.插件说明
[Github地址]https://github.com/2696524545/plugin/blob/main/README.md

### 7.插件购买地址
[Gin-Vue-Admin插件市场]https://plugin.gin-vue-admin.com/#/layout/newPluginInfo?id=42 

### 8.常见问题解答
[KubeConfig及Token凭据如何创建?]https://github.com/2696524545/plugin/blob/main/KubeConfig-Or-Token-Create.md

[Prometheus Operator 快速部署?]https://github.com/2696524545/plugin/blob/main/Prometheus-Operator.md

[Prometheus 数据查询过多，返回数据较大，导致Gin-Vue-Admin 操作日志会写入失败?]
```
Prometheus 数据查询过多，返回数据较大，导致Gin-Vue-Admin 操作日志会写入失败，修改字段类型：
表名:
   sys_operation_records
 
  字段名：  resp  修改为 longtext 类型

```
[MonacoEditor YAML 编辑器 鼠标定位不准问题？]
由于字体兼容性问题， 编辑器光标位置错误，解决办法：
注释全局font-family，文件路径 src/style/main.scss
![81B5DA41-8AB0-4DAE-8BB0-D0DA17E043FE](https://user-images.githubusercontent.com/5716348/221067415-735b0f41-406a-4678-a94d-b55add5a7b2c.png)

[Kruise Rollouts 金丝雀发布最佳实践]https://github.com/openkruise/rollouts/blob/master/docs/tutorials/basic_usage.md
[Kruise Rollouts 多批次发布最佳实践]https://openkruise.io/rollouts/user-manuals/strategy-multi-batch-update
[Kruise Rollouts A/B 测试发布策略最佳实践]https://openkruise.io/rollouts/user-manuals/strategy-ab-testing

[kubeskoop-exporter YAML 文件]https://github.com/2696524545/plugin/blob/main/kubeskoop-exporter.yaml

### 9.功能展示

### 新功能 (Kubevela 集群关联)
![集群关联](https://github.com/2696524545/plugin/blob/main/集群关联1.png?raw=true)
![集群关联列表](https://github.com/2696524545/plugin/blob/main/集群关联2.png?raw=true)
![集群注册](https://github.com/2696524545/plugin/blob/main/集群关联3.png?raw=true)

### 新功能 (Kubevela 应用管理)
![应用管理](https://github.com/2696524545/plugin/blob/main/Kubevela应用管理.png?raw=true)

### 新功能 (Kubevela 应用详情)
![应用详情](https://github.com/2696524545/plugin/blob/main/应用详情.png?raw=true)
![应用详情](https://github.com/2696524545/plugin/blob/main/应用详情2.png?raw=true)
![应用详情](https://github.com/2696524545/plugin/blob/main/应用详情3.png?raw=true)


### (KubeBlocks 中间件列表)
![容器文件管理](https://github.com/2696524545/plugin/blob/main/KubeBlockss-list.png?raw=true)

###  (KubeBlocks 中间件创建)
![容器文件管理](https://github.com/2696524545/plugin/blob/main/KubeBlocks-create.png?raw=true)


### 容器文件管理
![容器文件管理](https://github.com/2696524545/plugin/blob/main/容器文件管理.png?raw=true)

### 集群凭据管理
![集群凭据](https://github.com/2696524545/plugin/blob/main/集群凭据.png?raw=true)

### 集群用户管理
![集群用户管理](https://github.com/2696524545/plugin/blob/main/集群用户.png?raw=true)

### 集群权限管理
![集群权限](https://github.com/2696524545/plugin/blob/main/集群权限.png?raw=true)


### 终端审计管理
![终端审计](https://github.com/2696524545/plugin/blob/main/终端审计.gif?raw=true)

### （Kruise Rollouts 多批次发布）

![多批次发布](https://github.com/2696524545/plugin/blob/main/Kruise-Rollouts.gif?raw=true)

### （Pod TCP 指标监控）

![Pod TCP指标监控](https://github.com/2696524545/plugin/blob/main/podTcpMonitor.png?raw=true)

### （Pod 指标监控缩略图）

![Pod TCP指标监控](https://github.com/2696524545/plugin/blob/main/PodMonitor.gif?raw=true)

![集群管理](https://github.com/2696524545/plugin/blob/main/clusters.png?raw=true)
#### 集群管理
![集群管理](https://github.com/2696524545/plugin/blob/main/clusters.png?raw=true)
![集群管理](https://github.com/2696524545/plugin/blob/main/clusters3.png?raw=true)
#### 节点管理
![节点管理](https://github.com/2696524545/plugin/blob/main/node.png?raw=true)
#### 节点监控
![节点监控](https://github.com/2696524545/plugin/blob/main/nodemonitor.png?raw=true)
#### 工作负载
![工作负载](https://github.com/2696524545/plugin/blob/main/workloads.png?raw=true)
![工作负载](https://github.com/2696524545/plugin/blob/main/workload-form.png?raw=true)
#### Deployment详情
![Deployment详情](https://github.com/2696524545/plugin/blob/main/DeploymentDetail.png?raw=true)
#### Deployment编辑
![Deployment编辑](https://github.com/2696524545/plugin/blob/main/resourceEdit.png?raw=true)
#### Pod监控
![Pod监控](https://github.com/2696524545/plugin/blob/main/podmonitor.png?raw=true)
#### Pod终端
![Pod终端](https://github.com/2696524545/plugin/blob/main/PodTerminal.png?raw=true)
#### Pod终端日志
![Pod终端日志](https://github.com/2696524545/plugin/blob/main/podlogs.png?raw=true)
#### Pod管理
![Pod管理](https://github.com/2696524545/plugin/blob/main/Pods.png?raw=true)
#### 命名空间管理
![命名空间管理](https://github.com/2696524545/plugin/blob/main/namespaces.png?raw=true)
#### 网络管理
![网络管理](https://github.com/2696524545/plugin/blob/main/networks.png?raw=true)
![网络管理](https://github.com/2696524545/plugin/blob/main/network-form.png?raw=true)
#### 配置管理
![配置管理](https://github.com/2696524545/plugin/blob/main/configs.png?raw=true)
![存储管理](https://github.com/2696524545/plugin/blob/main/storages.png?raw=true)
#### 访问控制
![访问控制](https://github.com/2696524545/plugin/blob/main/access.png?raw=true)


## Star History

[![Star History Chart](https://api.star-history.com/svg?repos=2696524545/plugin&type=Date)](https://star-history.com/#2696524545/plugin&Date)
