# 展示效果
![访问地图](https://github.com/2696524545/plugin/blob/main/dataanalysis.png?raw=true)
# 1.前端插件安装
### 依赖插件
```
    npm i  install .
```
### 地图数据json文件放置
```
cp -r json src/assets/
```
### 地图数据json获取
```
https://datav.aliyun.com/portal/school/atlas/area_se
勾选，包含子区域，下载该json即可(当然该插件会包含地图json文件)
https://geo.datav.aliyun.com/areas_v3/bound/100000_full.json
```

# 2.后端插件安装
```

插件放入gin-vue-admin/server/plugin，后端插件引入

在gin-vue-admin/server/initialize/plugin.go 导入包

"github.com/flipped-aurora/gin-vue-admin/server/plugin/dataanalysis"

InstallPlugin 方法内，引入插件

PluginInit(PrivateGroup, dataanalysis.CreateDataAnalysisPlug()) // 访问ip数据分析插件


```

# 3 IP信息来源
```
http://ip-api.com/json/8.8.8.8?lang=zh-CN&fields=countryCode,country,regionName,city,zip,lat,lon,isp,org,as

```
