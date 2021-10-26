---
id: user-delete-tool
sidebar_position: 2
---

# 用户删除工具

## 简介[](#jian-jie)

配合[用户删除管理](/op/v/2.0/product-manual/customer-data-platform/datasource/yong-hu-shan-chu-guan-li#step-2-bian-geng-shan-chu-zhuang-tai)，将待删除的用户记录进行物理删除。

使用前，请先阅读[辅助工具](/op/v/2.0/developer-manual/toolbox#gong-neng-bian-jie-huo-yue-shu)的内容介绍。


## 功能说明[](#gong-neng-shuo-ming)

### 延迟删除[](#yan-chi-shan-chu)

执行如下命令后，GrowingIO前端用户删除管理中提交的待删除用户的记录，状态会立即变为删除中。

```sh
python3 clear_user.py -m clear_users
```

”删除中“状态的用户记录，会由系统的每日调度任务运行时，执行物理删除。


### 立即删除[](#li-ji-shan-chu)

执行如下命令后，GrowingIO前端用户删除管理中提交的待删除用户的记录，会立即执行物理删除的操作。

```sh
python3 clear_user.py -m clear_users -n true
```