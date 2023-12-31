---
id: get-space-users
sidebar_position: 1
---

# 空间成员列表

## 接口说明

查询空间下所有成员

## 接口地址

```
http://{api-host}/v2/api/enterprise/{enterpriseId}/projects/{projectId}/spaces/{spaceId}/users
```

## 请求方式

GET

## 请求参数

| 名称 | 类型 | 必填 | 描述 | 示例值 |
| ---- | ---- | ---- | ---- | ------ |
| enterpriseId | string | 是 | 企业ID | WlGk4Daj |
| projectId | string | 是 | 项目ID | WlGk4Daj |
| spaceId | string | 是 | 空间ID | WlGk4Daj |

提示：企业 ID 从企业概览中获取; 项目 ID 从项目概览中获取; 空间 ID 从空间概览中获取

## 公共请求参数

[公共请求参数](../../../open-api#公共请求参数)

## 返回数据

| 名称 | 类型   | 描述     |
| ---- | ------ | -------- |
| id   | string | 成员 ID  |
| name | string | 成员名称 |
| phone | string | 手机号 |
| email | string | 邮箱 |

## 返回示例

```
[
  {
    "id": "NxDLNp7E",
    "name": "demo",
    "phone": "1234567890",
    "email": "demo@demo.co"
  }
]
```
