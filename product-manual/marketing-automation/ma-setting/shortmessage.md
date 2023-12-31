---
id: shortmessage
sidebar_position: 3
---

# 短信触点管理

## 功能概述

在系统识别触发时机之后，要想对消费者进行触达离不开营销触点的接入。智能运营产品提供多种多样化的营销触点，通过全渠道触达实现消费者的精细化运营。注：触点管理是以分析云项目为维度的。

邮件作为实现自动化运营场景最常用的一种触达方式，是智能运营平台必须要具备的能力，您可在此处接入短信触点，接入成功后可实现短信的发送。当前支持「阿里云」短信渠道，为了顺利完成短信触点的接入，请您事先在阿里云短信平台开通账号

## 添加通道
进入短信列表，点击右上角【添加通道】按钮跳转出配置通道信息的弹窗，按照要求填写相关参数信息。
![图 1](/img/ae847297cb322e138641c182e473cdda427799c605eb7f5b1f07641f7060caaa.png)  
- 基本信息
- - 通道名称：输入通道名称作为运营在配置策略时识别通道的标识，限制在15个字以内；
- - 通道类型：选择通道类型，当前支持阿里云短信渠道
- - 描述：输入通道描述，非必填；
- 通道参数：调用第三方短信厂商必传的参数信息，
- - 厂商不同参数也不同，阿里云渠道所需accessKeyId以及accessKeySecret；
- - 请您事先在阿里云短信平台开通账号并申请该参数后进行配置，务必保证正确性，以此才能完成通道的接入；
- 短信发送配置：
- - 目标手机号：发短信必须向厂商传手机号字段，在此处可选择触达时所取的手机号字段，可选范围为已上报到数据中心的所有用户身份和属性。

## 通道列表
通道添加成功后可返回列表查看已添加的通道，并进行空间授权、通道编辑、通道测试、禁用以及删除操作。
![图 2](/img/d2007fb4756b89e8e607f229269431939f7956d597d51486cd00207bb9a0382d.png)  
### 空间授权
通道配置完成之后默认是开启的状态，此时是未授权给任何一个空间的，需要进行一步空间授权操作，确定该分析云项目下哪几个空间能够使用该通道进行触达策略的配置。
![图 3](/img/7c5ab266b47827c70c81ecdcc4a3482edbd93b9c7838d1a5a8836d4e312b26e6.png)  
### 通道编辑
- 通道禁用后才可以进行信息的编辑，启用状态下编辑按钮置灰；
- 通道编辑时通道类型无法进行修改。
### 通道测试
为了验证通道配置是否成功，当在画布中使用该通道是否真的能将消息发出去，我们提供了通道测试的能力。
![图 5](/img/368549042a28a8d1226ca77560097b1e430f1e79859781e7d40c426ac51f6537.png)  
- 手机号：输入要进行测试的手机号，每次测试仅能输入1个号码；
- 发送内容：选择要发送的内容，例如短信通道的短信模版；
- 发送测试：点击【发送测试】按钮系统会调用厂商通道发送短信，您可以关注是否有收到短信，若一直未收到短信通知，可能出现的原因：
- - 网络延迟或手机号已屏蔽短信等号码因素；
- - 在阿里云短信平台的账户余额不足，无法发短信；
- - 通道配置时通道参数错误，与实际在厂商申请的不一致；
- - 当号码及短信策航商因素排除后依然无法收到短信，请联系gio技术支持排查。
### 禁用
通道禁用及无法在流程画布中配置策略时选择该通道。
- 当该通道被进行中、暂停中、未开始、待发布、审批中状态的活动所引用时，无法进行禁用。
#### 删除
将通道禁用后可进行删除操作，启用中的通道删除按钮置灰，无法直接删除。

## 通道应用
通道配置完成并且测试成功之后，可在授权的空间内配置短信下发策略。
![图 1](/img/2b45e3b9e66463f4aaa957d77912ecbee09f2c2e9270d4d624062ebbdd51ab1b.png)  
