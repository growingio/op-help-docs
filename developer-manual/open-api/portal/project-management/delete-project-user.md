---
id: delete-project-user
sidebar_position: 8
---

# 移除项目成员

## 接口说明

移除项目成员

## 接口地址

```
http://{api-host}/v2/api/enterprise/{enterpriseId}/projects/{projectId}/users-remove
```

## 请求方式

DELETE

## 请求参数

| 名称 | 类型 | 必填 | 描述 | 示例值 |
| ---- | ---- | ---- | ---- | ------ |
| enterpriseId | string | 是 | 企业ID | WlGk4Daj |
| projectId | string | 是 | 项目ID | WlGk4Daj |
| userIds | string[] | 是 | 用户ID 列表 | ["WlGk4Daj", "WlGk4Daj"] |

提示：企业 ID 从企业概览中获取; 项目 ID 从项目概览中获取

## 公共请求参数

[公共请求参数](../../../open-api#公共请求参数)

## 请求示例

```
{
  "userIds": [
    "WlGk4Daj"
  ]
}
```


## 返回数据

| 名称 | 类型   | 描述     |
| ---- | ------ | -------- |
| data   | boolean | 请求结果 |

## 返回示例

```
{
  "data": true
}
```
