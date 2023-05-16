---
id: account
sidebar_position: 3
---

# 帐号管理

账号管理功能使用者一般为IT部门，可以统一管理企业账号，包含：

* 新建账号
* 编辑账号信息
* 解绑安全设备
* 重置密码
* 加入项目
* 删除账号

## 系统角色

| 角色名称 | 描述 |
| ---| --- |
| 企业拥有者 | 企业拥有者是系统最高权限人，拥有所有功能权限。仅可设置一人 |
| 企业管理员 | 企业管理员拥有所有的企业管理权限，包括项目及空间配额的管理。推荐将公司的IT运维部门的同事设置为此角色
| 企业成员 | 企业成员无企业管理权限，可加入项目使用产品功能。推荐将业务人员设置为此角色。
|GIO技术顾问| GIO专属技术顾问，帮助排查问题与进行系统运营维护升级工作。

## 功能说明

### 新建帐号

必要信息：

* 用户名
* 密码
* 企业角色：企业管理员、企业成员
* 项目设置
  * 可选择的项目范围：当前操作人可管理的项目范围。
  * 项目下可选择的空间范围：当前操作人可管理的空间范围。
* 姓名

可选信息：

* 手机号
* 邮箱

![图 2](/img/xinjianzhanghao_account.png)  

### 编辑账号信息

不可编辑：

* 用户名

可编辑：

* 企业角色
* 姓名
* 手机号
* 邮箱

![图 4](/img/bianjizhanghaoxinxi_account.png)  

### 解绑安全设置

仅当企业设置中开启【个人安全设置】，且包含MFA设备时有效。

![图 3](/img/jiebanganquanshebei_account.png)  

### 重置密码

系统将为选中账号自动生成复杂密码，不支持自定义密码。

![图 7](/img/chongzhimima_account.png)  

### 加入项目

* 可选择的项目范围：当前操作人可管理的项目范围。
* 项目下可选择的空间范围：当前操作人可管理的空间范围。

![图 6](/img/jiaruxiangmu_account.png)  

### 删除账号

账号删除后，选中帐户将无法登录。

![图 5](/img/shanchuzhanghao_account.png)  