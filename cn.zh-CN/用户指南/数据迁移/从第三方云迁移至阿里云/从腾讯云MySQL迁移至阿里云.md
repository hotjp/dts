# 从腾讯云MySQL迁移至阿里云 {#concept_52904_zh .concept}

本文介绍腾讯云MySQL数据库迁移到阿里云的步骤及注意事项。

## 前提条件 {#section_vw2_ycl_5fb .section}

-   已经[创建阿里云RDS实例](../cn.zh-CN/RDS for MySQL 快速入门/创建RDS for MySQL实例.md#)。
-   已经[创建拥有读写权限的账号](../cn.zh-CN/RDS for MySQL 快速入门/初始化配置/创建账号和数据库.md#)。

## 迁移限制 {#section_ang_jbk_5fb .section}

-   结构迁移不支持 event 的迁移。
-   对于MySQL的浮点型float/double，DTS通过round\(column,precision\)来读取该列的值，若列类型没有明确定义其精度，对于float，精度为38位，对于double类型，精度为308，请先确认DTS的迁移精度是否符合业务预期。
-   如果使用了对象名映射功能后，依赖这个对象的其他对象可能迁移失败。
-   当选择增量迁移时，源端的 MySQL 实例需要按照要求开启 binlog。
-   当选择增量迁移时，源库的 binlog\_format 要为 row。
-   当选择增量迁移且源 MySQL 如果为 5.6 及以上版本时，它的 binlog\_row\_image 必须为 full。

**说明：** 参数的修改可以在**数据库管理** \> **参数设置**里进行修改。

## 注意事项 {#section_ypx_pbk_5fb .section}

对于七天之内的异常任务，DTS会尝试自动恢复，可能会导致迁移任务的源端数据库数据覆盖目标实例数据库中写入的业务数据，迁移任务结束后务必将DTS访问目标实例账号的**写权限**用`revoke`命令回收掉。

## 操作步骤 {#section_uwt_sck_5fb .section}

1.  登录腾讯云MySQL数据库实例，查看详情页面的外网地址，包括域名和端口。

    **说明：** 若未开启外网地址，请单击**开启**并在弹出的对话框中单击**确定**。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63394/155866436531838_zh-CN.png)

2.  登录[DTS控制台](https://dts.console.aliyun.com/)。
3.  在左侧菜单栏单击**数据迁移**，单击右上角**创建迁移任务**。
4.  填写源库和目标库信息，具体参数配置说明如下：

    |库类别|参数|说明|
    |---|--|--|
    |源库|实例类型|源库实例类型，这里选择有公网IP的自建数据库。|
    |实例地区|如果您的实例进行了访问限制，请先放开对应地区公网IP段的访问权限后，再配置数据迁移任务。 **说明：** 可以单击右侧**获取DTS IP段**查看、复制对应地区的IP段。

 |
    |数据库类型|源数据库类型，这里选择MySQL。|
    |主机名或IP地址|腾讯云数据库的外网地址的域名部分。|
    |端口|腾讯云数据库的外网地址的端口部分。|
    |数据库账号|腾讯云数据库的默认高权限账号：root。|
    |数据库密码|腾讯云数据库root账号的密码。|
    |目标库|实例类型|目标实例的类型，这里选RDS实例。|
    |实例地区|目标实例的地区。|
    |RDS实例ID|对应地区下的实例ID，这里选择想要迁移到的目标实例的ID。|
    |数据库账号|目标实例的拥有读写权限的账号。|
    |数据库密码|目标实例的对应账号的密码。|
    |连接方式|有**非加密传输**和**SSL安全连接**两种连接方式，选择SSL安全加密连接会显著增加CPU消耗。|

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63394/155866436531840_zh-CN.png)

5.  填写完毕后单击**测试连接**，确定源库和目标库都测试通过。
6.  单击**授权白名单并进入下一步**。
7.  勾选对应的迁移类型，在迁移对象框中将想要迁移的数据库选中，单击![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/79929/155866436540698_zh-CN.png)移动到已选择对象框。

    **说明：** 为保证迁移数据的一致性，建议选择结构迁移+全量数据迁移+增量数据迁移。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63394/155866436531841_zh-CN.png)

8.  单击**预检查并启动**，等待预检查结束。

    **说明：** 如果检查失败，可以根据错误项的提示进行修复，然后重新启动任务。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63394/155866436531845_zh-CN.png)

9.  单击**下一步**，在**购买配置确认**对话框中，勾选**《数据传输（按量付费）服务条款》**并单击**立即购买并启动**。

    **说明：** 结构迁移和全量迁移任务暂不收费，增量迁移根据链路规格按小时收费。

10. 等待迁移任务完成即可。

    ![](http://static-aliyun-doc.oss-cn-hangzhou.aliyuncs.com/assets/img/63394/155866436531844_zh-CN.png)


