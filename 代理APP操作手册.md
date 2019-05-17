[TOC]

### 一、测试环境

当前我们测试环境为staging环境，测试网址请点击[官网](http://pre.e-ports.com/zh-Hans-CN/)，[易船代](http://pre.e-ports.com/zh-Hans-CN/login)。

-------

### 二、测试账号

以下是我们在staging环境使用的测试账号：

| 账号 | 密码 | 权限 | 角色 | 备注 |
| ---- | ---- | ---- | ---- | ---- |
| MarketQingdao@agent.com | password | 超级管理员 | 代理方 | 代理服务港口青岛 |
| qingdao@test.com | 123456 | 外勤 | 代理方 | 青岛外勤账号 |
| qingdao@app.com | 123456 | 外勤 | 代理方 | 青岛外勤账号 |
| MarketQingdao@principal.com | password | 超级管理员 | 委托方 | 委托1 |
| MarketShanghai@agent.com | password | 超级管理员 | 代理方 | 代理服务港口上海|
| testApp@163.com | 123456 | 外勤 | 代理方 | 上海外勤账号 |
| testApp@agenct.com | 123456 | 外勤 | 代理方 | 上海外勤账号 |
| MarketShanghai@principal.com | password | 超级管理员 | 委托方 | 委托2 |

-------

### 三、代理APP下载

#### 1. 代理APP Android版本下载

1.打开[易船代](http://pre.e-ports.com/zh-Hans-CN/login)，输入易船代账号，登录成功后，光标移到首页的右上方的APP图标（如下图）：

![图标](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551670561384.png)

界面上会弹出APP下载的二维码（如下图）：

![1551670752921](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551670752921.png)

用Android手机扫描该二维码后，完成APP的下载安装（具体描述，略）；下载完成后，手机上会新增一个APP，图标如下图：

![1551671083992](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551671083992.png)

如果有这个logo的APP，说明APP安装成功。

#### 2. 代理APP IOS版本下载

待续……

-------

### 四、有外勤参与的服务项和服务节点

以下是需要外勤参与的服务项和服务项节点

| ID | 服务项 | 外勤参与节点 | 备注 |
| ---- | ---- | ---- | ---- |
| 1 | Crew Change On Signer | 预计上船时间，船员已接到、等待办理上船手续，手续办理完毕，准备送船员上船 | |
| 2 | CTM  | 准备送船 | |
| 3 | Port Captain | 口岸船长准备上船，口岸船长准备下船 | |
| 4 | Inward Formality | 等待船舶靠泊、等待联检中、等待办理进港手续 |  |
| 5 | Outward Formality | 等待办理离港证、外勤准备返还离港证，等待联检和商检上船、等待引水上船、等待开航 | |
| 6 | PSC Inspection | 等待PSC上船检查，等待确认PSC检查结果 | |
| 7 | Tech/Supt Attenfance | 准备接机，机务已接到，准备办理上船手续，送机务上船，送机务离开 | |
| 8 | Renew Certificate-health | 准备接送船员检查 | |
| 9 | Renew Certificate-SSCEC | SSCEC检查中 | |
| 10 | Renew Certificate-Vaccination | 准备接送船员检查 | |
| 11 | Off Land | 安排提货 | |
| 12 | Medical Service | 准备安排外勤接船人员，船员就医中 | |
| 13 | Crew Change Off Signer | 签证办理中，已获得签证，准备办理下船手续，接船员下船中，送船员离开 | |
| 14 | Water Sample Analysis | 等待取水样 | |
| 15 | Spare Part Delivery | 等待备件送船 | |

-------

#### 五、操作流程

当前代理APP操作，需要和Web端配合，大致流程如下：

```flow
str=>start: 代理方存在一个新生成的订单
e=>end: 完成
op1=>operation: Web端指派外勤A
op2=>operation: Web端操作服务项
op3=>operation: APP端操作服务
op4=>operation: 外勤服务项

cd1=>condition: 是否外勤操作服务项？
cd2=>condition: 是否外勤操作节点？
cd3=>condition: 是否完成？

str->op4->op1->cd2
cd2(yes)->op3->cd3
cd2(no)->op2->cd3
cd3(no)->cd2
cd3(yes)->e
```

#### 六、操作步骤



##### 1. Web端指派外勤操作

当前在WEB端有15个服务项需要外勤操作，在这里不做一一描述了，具体需求请查看[需求 584](http://zendao.e-ports.com/zentao/story-view-584.html)。在这里以服务项**Crew Change On Signer**为例进行说明。

**预置条件**：代理方有一个询价后新生成的订单A，且该订单中包含服务项**Crew Change On Signer**；

- 使用**代理方管理员账号**登录[易船代](http://pre.e-ports.com/zh-Hans-CN/login)，进入订单A的详情界面，服务项**Crew Change On Signer**的状态为**等待开始**；

![1551682792228](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551682792228.png)



- 点击Boarding列中服务项**Crew Change On Signer**对应的值，弹出外勤选择框；

![1551682815488](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551682815488.png)

- 选择完成后，返回查查看。

  ![1551683391536](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551683391536.png)

  当前Boarding的值为指派的人员。

-------

##### 2. 外勤操作任务

预置条件：服务项**Crew Change On Signer**已指派成功

- APP端的任务列表中新增一条任务，状态为**未开始**，且消息列表中新增一条消息；

![1551683856194](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551683856194.png)

![1551683917618](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551683917618.png)

- 未到节点**预计上船时间**前，APP端无法操作（这部分流程由Web端完成），进入任务详情界面，没有提交按钮；

  ![1551684136371](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551684136371.png)

- 当状态变为**预计上船时间**时，APP端的任务状态变为**进行中**，消息列表中新增一条消息；

  ![1551684368194](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551684368194.png)

  ![1551684396560](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551684396560.png)

- 进入任务详情界面可以进行提交操作，后续几步操作，都是如此，不再详述；

  ![1551684435672](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551684435672.png)

- 当任务的几个节点操作完成后，任务状态变为**已完成**；

  ![1551684693872](C:\Users\xu_bob\AppData\Roaming\Typora\typora-user-images\1551684693872.png)

  **注意：此时Web端的服务项不一定是已完成状态，因为有可能该服务项后续只需要内勤继续操作，不需要外勤操作了。**

-------
