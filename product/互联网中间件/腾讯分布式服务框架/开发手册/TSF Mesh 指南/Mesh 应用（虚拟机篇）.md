## 准备工作
1. 下载 TSF 提供的 Demo。该步骤预计耗时 1 min。
2. 在 TSF 控制台上已创建容器集群并添加节点，参考 [集群](https://cloud.tencent.com/document/product/649/13684)。对于未创建容器集群和添加节点的用户，该步骤预计耗时 10 min。
3. 主机上已安装应用运行的环境（如 Python 应用的相关依赖等） 。该步骤预计耗时根据运行环境的复杂度有所不同。


## 一、创建并部署 Mesh 应用
### 1. 创建应用
1.1 登录 TSF 控制台。
1.2 单击左侧导航【[应用管理](https://console.cloud.tencent.com/tsf/app)】。
1.3 单击【新建应用】，并填写以下信息：
   - 应用名：填写应用名称
   - 部署方式：选择 **虚拟机部署**  
   - 应用类型：选择 **Mesh 应用**

1.4 单击【提交】按钮，完成应用创建。


### 2. 上传程序包
2.1 单击【程序包管理】标签页。
2.2 单击【上传程序包】按钮，选择程序包，填写程序包相关信息。
2.3 单击【提交】按钮，完成上传。
   ![](https://main.qcloudimg.com/raw/5feffece169465b33226d89a4d1b8db3.png)



### 3. 创建部署组
可参考 [虚拟机应用部署组](https://cloud.tencent.com/document/product/649/15524) 中关于 **创建部署组** 的流程描述，此处不再赘述。



### 4. 部署应用

可参考 [虚拟机应用部署组](https://cloud.tencent.com/document/product/649/15524) 中关于 **部署应用** 的流程描述，此处不再赘述。



## 二、查看服务是否注册成功

1. 单击左侧导航栏【服务治理】，查看服务是否注册成功。如果注册成功，服务显示在线状态。
   ![](https://main.qcloudimg.com/raw/b04f1d3145a472e8a76423a3ce98a9df.png)
2. 单击服务 ID，进入服务详情页。单击【API 列表】标签页，可以查看上报的 API 定义。
   ![](https://main.qcloudimg.com/raw/66d48d0a42ebb521c83e3e25070247d2.png)


## 三、验证服务调用
使用同样的步骤一和步骤二部署 user、shop 和 promotion 三个应用，并创建服务与应用关联。注意在创建三个服务时的端口号：
- user 端口号：8091
- shop 端口号：8092
- promotion 端口号：8093

用户可以登录容器集群 VPC 下任一机器，然后通过 `curl` 命令验证 user 服务是否健康，以及触发 user 服务调用 shop 和 promotion 服务。

#### 1. 触发 user 服务调用 shop 和 promotion 服务

user、shop、promotion 三个服务的接口间调用关系如下：

user (`/api/v6/user/account/query` )  => shop (`/api/v6/shop/order`) => promotion (`/api/v6/promotion/query`)

为了验证 user 服务能通过服务名来调用 shop 服务，需要用户通过以下几种方式来触发 user 服务的接口调用：

- 登录 user 所在云服务器，在服务器上执行如下 `curl` 命令。
```
curl localhost:8091/api/v6/user/account/query
```
- **API 网关**：用户可以通过在 API 网关配置微服务 API 来调用 `user` 服务的接口。关于如何配置微服务 API 网关，可参考文档 [API 网关作为请求入口](https://cloud.tencent.com/document/product/649/17644)。



#### 2. 在控制台验证服务间是否调用
可以通过几种方式验证服务是否成功被 sidecar 代理注册到注册中心，同时服务之间是否成功地进行了调用。
- **服务治理** 界面：选择集群和命名空间后，如果服务列表中的服务状态为 **在线** 或 **单点在线**，表示服务被代理注册成功。如果服务提供者的请求量大于 0，表示服务提供者被服务消费者请求成功。
  ![](https://main.qcloudimg.com/raw/89040e8ddf377a1a9a972cac02b65037.png)
- **依赖拓扑** 界面：选择集群和命名空间后，调整时间范围覆盖服务运行期间的时间范围，正常情况下，将出现服务之间相互依赖的界面。
  ![](https://main.qcloudimg.com/raw/95c0a6e134664254b23e2c70e5f25671.png)
