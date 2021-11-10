---
id: event-property
sidebar_position: 3
---

# 事件属性

对事件属性进行声明和管理

## 简介[](#jian-jie)

事件属性是代码埋点事件的重要信息。

根据设置的类型不同，事件属性在统计分析中常用来做计算的指标或拆解的维度。

> 举例：某教育行业客户的用户在线预约课程的事件，需要按照营销活动名称、课程门店分析课程预约的数量，可以将营销活动名称、课程门店名称创建为事件属性，并关联到预约课程的事件上，以获得相应维度分析的能力。


## 功能说明[](#gong-neng-shuo-ming)

### 创建事件属性[](#chuang-jian-shi-jian-shu-xing)

一、在客户数据平台模块中的“事件管理 > 事件属性”，进入事件属性管理页面。

![](https://gblobscdn.gitbook.com/assets%2F-M2qbZInaXgdm8kkNosp%2F-M3ENsQm3QGQGowP3MJb%2F-M3ENvF2u7JNkBUnrmS5%2Fimage.png?alt=media&token=b18ba536-b466-4a34-93e1-a4a522d0e40a)

事件属性管理页面

二、单击右上角**添加事件属性**，进入**新建事件属性**页面

![](https://gblobscdn.gitbook.com/assets%2F-M2qbZInaXgdm8kkNosp%2F-M3ENsQm3QGQGowP3MJb%2F-M3EOIB8LJVUmxvr5Ni1%2Fimage.png?alt=media&token=8138b8a6-de6c-4e9c-817b-da5e646bdec6)

事件属性新建页面

| 参数  | 说明  |
| --- | --- |
| 名称  | GrowingIO平台上事件属性的名称。 |
| 标识符 | 此属性在代码中的标识，仅允许大小写英文、数字、下划线，并且不能以数字开头。 |
| 类型  | 可选类型(单选)：字符串、整数、小数（事件属性创建成功后，属性类型不可更改） |
| 描述  | 事件属性的描述，可填写对应触发时机和应用场景，或对属性值进行举例。 |

三、填写完成后单击**确定**，完成一个事件属性的创建。

> 在完成了事件属性配置，事件关联，以及正确的代码实施后。我们即可开始在GrowingIO使用事件属性。


### 管理事件属性[](#guan-li-shi-jian-shu-xing)

在事件属性管理页面可以查看事件属性的名称、标识符、类型、创建日期、创建人。

您也可以对事件属性进行以下操作：

**搜索：**您可以在页面中列表上方的搜索框按事件属性名称和标识符来搜索事件属性。

**QuickView：**单击任一事件属性，您可以在右侧弹出的事件属性详情中，查看事件属性的基本信息。

**编辑：**在QuickView界面单击事件属性的参数进行修改，修改后单击保存。

**删除：**单击单条事件属性右侧的 ![](https://docs.growingio.com/.gitbook/assets/-Lo08UtW7H58ehFKeZ4g-LsycTyZaItbL8_Wigcx-LsyfkaafJ-8X2utJ9BbE782B9E782B9E782B9.png) 选择删除，可删除不需要的事件属性。

**批量操作**：在列表中使用复选框选择多个事件属性，可以进行批量删除。