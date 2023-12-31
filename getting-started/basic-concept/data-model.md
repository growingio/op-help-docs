---
id: data-model
sidebar_position: 2
--- 

# 数据模型

## 简介[](#jian-jie)


GrowingIO采集的原始行为数据分为四种不同类型，分别为访问（visit）、页面（page）、埋点（custom_event）和行为（action）。

* 访问（visit）： 代表用户开启了一个新的访问会话，访问行为的生命周期与会话（session）绑定，新的session代表一个新的访问。
    
* 页面浏览（page）：代表用户的一次页面浏览，SDK采集以及发送浏览事件中的数据。
    
* 埋点（custom_event）： 代表用户触发了客户埋的点发送的事件。事件触发的时间以及发送的行为数据信息都由客户指定。
    
* 行为（action）： 代表用户操作了页面上的某些元素发送的行为，包括点击了按钮、修改了数据框的内容等等。
    

过去，GrowingIO数据平台将上述四种行为数据存储至多张表中，这种模型导致后续的统计逻辑非常复杂。为了串联多种行为数据，我们在查询时进行了多表关联计算，因此导致部分极端场景下数据计算结果不一致的问题偶有发生。另外，该种数据模型数据比较分散，没有统一的接口，不易与其他组件进行对接。

在[9.0版本](../../update-log#v2020902020年9月30日发布)后，我们将多种行为数据统一抽象为EVENT模型，即四种行为数据统一到一张表中。该种模型简化了平台计算逻辑，并且通过Thrift Server方便GrowingIO数据平台与其他组件进行对接，进而提升开发效率和降低维护成本。

最新的数据模型同时具备了提供实时数据的能力，行为数据从触点触发再到客户可以观察到，时间间隔一般在10秒以内。

## 客户操作手册[](#ke-hu-cao-zuo-shou-ce)

GrowingIO CDP为客户安装了Zeppelin，客户可以通过Zeppelin SQL解释器查询数据平台中的数据库、数据表和表中数据。

**SQL语法：**

查询所有的数据库：show databases

查询数据库中的数据表：show tables in {database_name}

查询数据表结构：desc {database\_name}.{table\_name}

查询数据表创建语句：show create table {database\_name}.{table\_name}

查询表分区：select partition from system.parts where database='{database_name}' and table='{table_name_}'

查询数据表数据： select {field} from {database\_name}.{table\_name}

## 数据表[](#shu-ju-biao)


事件表：event

用户表：user

用户融合日志表：id_mapping_log

## 事件表（event）：[](#shi-jian-biao-event)

GrowingIO通过SDK实时采集或文件批量导入的行为数据，统一存放在event表中。

事件表按照日期分区，数据的时效性为实时，即行为数据从触发采集到入库可查的时间间隔一般在10秒内。

在事件表中，您可以查询事件采集上报的全部信息。由于字段繁多，通过前缀将不同角度的字段分组提升查询体验，共分三组：

* 事件标识符、发生时间、用户ID等[基础信息](../../getting-started/basic-concept/data-model#基础信息)，字段无前缀
    
* 城市、浏览器、设备类型等[预定义属性](../../getting-started/basic-concept/data-model#预定义属性)，字段统一使用 $ 符号前缀
    
* 埋点上报的[自定义属性](../../getting-started/basic-concept/data-model#自定义属性)，字段统一使用 var_ 作为前缀
    

> 虽然事件表的字段按照前缀做了分组，但它们都在事件表内。

### 数据字段[](#shu-ju-zi-duan)

#### 基础信息[](#ji-chu-xin-xi)

| 字段名         | 字段类型 | 含义及示例                                                                               |
|----------------|----------|------------------------------------------------------------------------------------------|
| event_key      | String   | 事件标识符。示例：visit事件=$visit、页面浏览事件=$page、自定义事件如paySuccess等         |
| event_type     | String   | 事件类型。示例：visit、page、custom_event等                                              |
| event_time     | DateTime | 事件上报时间，毫秒。示例：2021-06-01 23:59:20.912<br></br>注：分析应用等场景的核心字段   |
| client_time    | DateTime | 事件发生时间，毫秒。示例：2021-06-01 23:59:20.912<br></br>注：仅用在访问时长和页面时长里 |
| event_id       | String   | 事件ID                                                                                   |
| anonymous_user | String   | 访问用户ID                                                                               |
| user           | String   | 登录用户ID                                                                               |
| user_key       | String   | 用户身份类型。示例：$basic_userId                                                        |
| gio_id         | UInt     | id-service为用户生成的自增序号，是分析中统计用户量等指标的关键字段                       |
| account_id     | String   | 账户ID（GrowingIO产品部署时自动生成，默认唯一值）                                        |
| dt             | Date     | 事件上报的日期。示例：2021-06-01                                                         |
| attributes     | Map      | 事件属性集。示例：{"city":"beijing","color":"red"}                                       |

event_id是对重复数据去重处理的主要参数之一。它的生成机制随事件类型的不同而各异。

* 访问事件：通过session字段加密生成
    
* 页面浏览事件：通过session、client_time和path字段加密生成
    
* 自定义事件：通过event_key、client_time、_anonymous_user、user、attributes等加密生成
    
#### 预定义属性[](#yu-ding-yi-shu-xing)

| 字段名              | 字段类型 | 含义及示例                                                           |
|---------------------|----------|----------------------------------------------------------------------|
| $session        | String   | 会话标识，标记一个访问                                                                   |
| $platform           | String   | 平台标识，示例：Web                                                  |
| $domain             | String   | 域名或包名                                                           |
| $path               | String   | 页面路径                                                             |
| $referrer_domain    | String   | 来源域名或包名                                                       |
| $referrer_path      | String   | 上一个页面路径                                                       |
| $query              | String   | 页面query参数                                                        |
| $xpath              | String   | 元素在页面中的位置                                                   |
| $text_value         | String   | 元素对应的文本名                                                     |
| $href               | String   | 元素对应的链接                                                       |
| $index              | String   | 元素在列表中的位置，0开始                                            |
| $url_scheme         | String   | 集成应用标识                                                         |
| $browser            | String   | 浏览器名称                                                           |
| $browser_version    | String   | 浏览器版本                                                           |
| $user_agent         | String   | 浏览器 agent 详细信息                                                |
| $os                 | String   | 操作系统名称                                                         |
| $os_version         | String   | 操作系统版本                                                         |
| $sdk_version        | String   | SDK版本号                                                            |
| $client_version     | String   | 客户端版本（仅移动端有效）                                           |
| $channel            | String   | 客户端渠道来源                                                       |
| $utm_source         | String   | 广告来源，标识投放的网站                                             |
| $utm_medium         | String   | 广告媒介或营销媒介                                                   |
| $utm_campaign       | String   | 广告名称，产品的具体广告系列名称、标语等                             |
| $utm_term           | String   | 广告关键字，标识付费搜索关键字                                       |
| $utm_content        | String   | 广告内容，用于区分相似内容或同一广告内的链接                         |
| $utm_source_session         | String   | 会话级广告来源，标识投放的网站                                             |
| $utm_medium_session         | String   | 会话级广告媒介或营销媒介                                                   |
| $utm_campaign_session       | String   | 会话级广告名称，产品的具体广告系列名称、标语等                             |
| $utm_term_session           | String   | 会话级广告关键字，标识付费搜索关键字                                       |
| $utm_content_session        | String   | 会话级广告内容，用于区分相似内容或同一广告内的链接                         |
| $traffic_source        | String   | 流量来源，如付费搜索、付费购物、自然搜索等                         |
| $traffic_source_session        | String   | 会话级流量来源，如付费搜索、付费购物、自然搜索等                         |
| $key_word           | String   | 访问来源广告关键字                                                   |
| $device_brand       | String   | 设备品牌名称                                                         |
| $device_model       | String   | 设备型号                                                             |
| $device_type        | String   | 设备类型                                                             |
| $device_orientation | String   | 设备方向                                                             |
| $language           | String   | 语言，示例：zh-cn                                                    |
| $country_code       | String   | 国家码，示例：CN                                                     |
| $country_name       | String   | 国家名称，示例：中国                                                 |
| $region             | String   | 地区，示例：江苏                                                     |
| $city               | String   | 城市，示例：南京                                                     |
| $ip                 | String   | 客户端IP地址                                                         |
| $data_source_id   | String   | 数据源信息                                                           |
| $page_count         | UInt32   | 访问深度，即一次访问的页面浏览量                                     |
| $duration           | UInt32   | 时长（秒）。page事件上是页面停留时长<br></br>，visit事件上是访问时长 |
| $bot_id   |  Int32  | 爬虫流量标识，NULL值为正常流量                                                       |
| $share_title   |  String  | 小程序端特有，分享收藏标题                                                       |
| $target   |  String  | 小程序端特有，触发控件。如果 from 值是 button，则 target 是触发这次转发事件的 button id，否则为 undefined题                                                       |
| $from   |  String  | 小程序端特有，记录转发事件来源。取值'button' 代表页面内转发按钮；取值'menu'，代表右上角转发菜单题                                                       |
| $query   |  String  | 小程序端特有，页面参数，取值逻辑：小程序页面路径的自定义参数部分。                                                       |
| $share_path   |  String  | 小程序端特有，分享（收藏）页面路径                                                   |
| $share_query   |  String  | 小程序端特有，分享（收藏）页面自定义参数部分 |
> $page_count和$duration是离线计算而非实时采集的信息，时效性是T+1天。

#### 自定义属性[](#zi-ding-yi-shu-xing)

| 字段名               | 含义及示例                                                                                |
|----------------------|-------------------------------------------------------------------------------------------|
| var_{事件属性标识符} | 比如为提交订单事件定义了“订单金额”的属性，标识符为money ，它在事件表中的字段为：var_money |
| vvar_{虚拟属性标识符} | 比如定了某虚拟属性“成本”的标识符为cost，它在事件表中的字段为：vvar_cost                                                                                     |

> 自定义属性字段的生成机制：[数据中心](../../product-manual/data-center) 中定义了新的事件属性，系统会实时触发事件表增加相应的字段，相应地当删除了某事件属性时，系统也会实时删除事件表中的该属性字段。

### 预置事件[](#yu-zhi-shi-jian)

预置事件是指不需要您代码埋点，只要您的产品集成了GrowingIO的客户端SDK，在自动采集的默认模式下，用户使用您的产品时，用户的大部分行为会默认采集上报，比如用户访问产品、浏览了页面以及点击页面的按钮等行为。一些预置事件则是GrowingIO系统离线计算得到的，以便于支持您使用跳出率、用户量等指标。

| 名称     | 标识符       | 含义及示例                                                           |
|----------|--------------|----------------------------------------------------------------------|
| 访问     | $visit       | 用户开启新的访问会话，记录一次访问                                   |
| 页面浏览 | $page        | 一次页面浏览                                                         |
| 访问退出 | $exit        | 一次访问结束。 注：使用会话中的最后一次页面浏览事件，T+1离线计算生成 |
| 访问跳出 | $bounce      | 一次访问中仅有1次页面浏览。 注：使用该页面浏览事件，T+1离线计算生成                      |
| 元素点击 | $view_click  | 页面上用户点击元素的事件，比如点击了“查看详情”的按钮                 |
| 元素修改 | $view_change | 页面上用户改变元素值的事件，比如修改了下拉框值                       |
| 表单提交 | $form_submit | 页面上用户提交表单的事件                                             |
| 深度链接唤醒事件     | $ads_reengage       | 用户点击深度链接唤醒APP时触发                                   |
| 小程序分享到朋友圈 | $mp_share_timeline  | 用户点击分享到朋友圈按钮时触发 |
|小程序添加收藏| $mp_add_favorites|  用户点击小程序添加收藏按钮时触发   |
|小程序分享事件 |$mp_on_share|   用户点击小程序转发分享按钮时触发  |
|广告激活|$ads_activation| 用户下载安装 App 后，首次启动时会上报|
|广告曝光|$ads_imp|当在投放广告平台的链接曝光（被用户看到）时，会触发$ads_imp事件|
|广告点击 | $ads_click |用户点击投放在广告平台的链接时，会触发$ads_click事件 |
| 任意事件 | $anyevent   | 事件表中的所有事件抽象得到，实时计算生成                             |

> 关于任意事件
> 
> * 核心使用场景：计算用户量，通过约减数据量来获得查询性能的提升
> * 生成机制：所有事件的部分字段分组统计得到，比如event_time字段换算到小时，attributes字段仅用到path等。
    

### 查询示例[](#cha-xun-shi-li)

```sql
-- 查询2021-06-01的用户量

select count(distinct gio_id) from event where dt='2021-06-01';

​

-- 查询2021-06-01的页面浏览量

select count(1) from event where dt='2021-06-01' and event_key='$page';

​

-- 查询2021-06-01的访问量

select count(distinct session) from event where dt='2021-06-01' and event_key='$visit';

​

-- 查询2021-06-01的总访问时长

select sum($duration) from event where dt='2021-06-01' and event_key='$visit';

​

-- 查询2021-06-01的跳出次数

select count(1) from event where dt='2021-06-01' and event_key='$bounce'

​

-- 查询2021-06-01的退出次数

select count(1) from event where dt='2021-06-01' and event_key='$exit'
```

## 用户表( user )：

在 user 表中，您可以查询系统中全部识别用户( gio_id )以及用户的

* 用户身份
* 用户属性
* 用户标签
* 群体画像
* 融合标记


### 用户身份

在 user 表中，您可以使用 **客户数据平台 > 用户管理 > 用户身份** 中定义的 **用户身份 标识符** 查询系统用户的用户身份值。**id_{标识符}** 和 **ids_{标识符}** 分别对应 user 表中的一列数据，其中：

| 列名 | 数值类型 | 业务含义 |
| -- | -- | -- |
| id_{标识符} | 字符串 | 最后识别的用户身份值 |
| ids_{标识符} | 列表 | 全部用户身份值 |

时效性：准实时
数据延时：2分钟以内

:::info 举例：
系统用户身份配置为

| 名称    | 标识符          | 识别方式    | 优先级 |
|---------|-----------------|-------------|--------|
| 用户 ID | $basic_userId   | 唯一身份 ID | 1      |
| 设备 ID | $anonymous_user | 弱身份 ID   | 2      |

一个用户先使用设备X注册并登陆账号u1，后在设备Y上登陆账号u1，此时 user 表中用户身份存储为

| gio_id | id_$basic_userId | ids_$basic_userId | id_$anonymous_user | ids_$anonymous_user |
|--------|------------------|-------------------|--------------------|---------------------|
| n      | u1               | ['u1']            | Y                  | ['X','Y']           |
:::

查询示例：

``` sql
-- 查询使用设备X的系统用户的全部 用户ID 和 设备ID

select
    gio_id
    ,ids_$basic_userId
    ,ids_$anonymous_user
from olap.user
where has( ids_$anonymous_user , 'X' )
```

| gio_id | ids_$basic_userId | ids_$anonymous_user |
|--------|-------------------|---------------------|
| n      | ['u1']            | ['X','Y']           |

### 用户属性

在 user 表中，您可以使用 **客户数据平台 > 用户管理 > 用户属性** 或 **项目 > 用户洞察 > 用户属性** 中定义的 **用户属性 标识符** 查询系统用户的用户属性值。**usr_{标识符}** 对应 user 表中的一列数据，其中：

| 列名         | 数值类型               | 业务含义   |
|--------------|------------------------|------------|
| usr_{标识符} | 由用户属性数值类型决定 | 用户属性值 |

时效性：实时

数据延时：秒级

> 用户属性新建和删除时，user表自动增加和减少对应数据列

查询示例：

``` sql
-- 查询系统中 用户属性 会员等级( member_type ) 为 VIP 的全部用户以及用户的 用户ID( $basic_userId )、姓氏( $basic_name )和性别( $basic_gender )

select
    gio_id
    ,id_$basic_userId
    ,left( usr_$basic_name , 1) as last_name
    ,usr_$basic_gender
from olap.user
where usr_member_type = 'VIP'
``` 

| gio_id | ids_$basic_userId | last_name | usr_$basic_gender |
|--------|-------------------|-----------|-------------------|
| n1     | m1                | 陈        | 男                |
| n2     | m2                | 张        | 女                |
| ...    | ...               | ...       | ...               |

### 用户标签

在 user 表中，您可以使用 **客户数据平台 > 用户管理 > 用户标签** 中定义的 **用户标签 标识符** 查询系统用户的用户标签值。**{标识符}** 对应 user 表中的一列数据，其中：

| 列名     | 数值类型               | 业务含义   |
|----------|------------------------|------------|
| {标识符} | 由用户标签数值类型决定 | 用户标签值 |

时效性：实时

数据延时：秒级

用户标签更新频率：每日更新

> 用户标签新建和删除时，user表自动增加和减少对应数据列

查询示例：

``` sql
-- 查询系统中 设备号为 X 的系统用户的 用户ID( $basic_userId )、会员等级( member_type ) 和 购买意向( tag_purchase_intention)

select
    gio_id
    ,id_$basic_userId
    ,usr_member_type
    ,tag_purchase_intention
from olap.user
where has( ids_$basic_userId , 'X' )
``` 

| gio_id | id_$basic_userId | usr_member_type | tag_purchase_intention |
|--------|------------------|-----------------|------------------------|
| n      | m1               | VIP             | 高                     |


### 用户分群

在 user 表中，您可以使用 **项目 > 用户洞察 > 群体画像** 中定义的 **群体画像 标识符** 查询系统用户是否属于该群体。**{标识符}** 对应 user 表中的一列数据，其中：

| 列名     | 数值类型 | 业务含义                                |
|----------|----------|-----------------------------------------|
| {标识符} | 整数     | 1 - 属于该群体<br/>0 - 不属于该群体 |

时效性：实时
数据延时：秒级
群体画像更新频率：每日更新 或 手动更新

> 群体画像新建和删除时，user表自动增加和减少对应数据列

查询示例：

``` sql
-- 查询系统中 属于用户群体 五一参加活动用户( seg_51activity ) 的全部用户以及用户的 用户ID( $basic_userId )、姓氏( $basic_name )、性别( $basic_gender )、会员等级( member_type ) 和 购买意向( tag_purchase_intention)

select
    gio_id
    ,id_$basic_userId
    ,left( usr_$basic_name , 1 ) as last_name 
    ,usr_$basic_gender
    ,usr_member_type
    ,tag_purchase_intention
from olap.user
where seg_51activity = 1
``` 

| gio_id | id_$basic_userId | last_name | usr_$basic_gender | usr_member_type | tag_purchase_intention |
|--------|------------------|-----------|-------------------|-----------------|------------------------|
| n1     | m1               | 陈        | 男                | VIP             | 高                     |
| n2     | m2               | 张        | 女                | 非会员          | 中                     |
| ...    | ...              | ...       | ...               | ...             | ...                    |

### 融合标记

在 user 表中，您可以使用 **is_merged** 查询用户是否被融合。

| 列名      | 数值类型 | 业务含义                        |
|-----------|----------|---------------------------------|
| is_merged | 整数     | 1 - 被融合<br/>0 - 未被融合 |

时效性：实时

数据延时：秒级

查询示例：

``` sql
-- 查询系统中 被融合用户数量

select
    count(distinct gio_id) as `user_merged`
from olap.user
where is_merged = 1
```

| user_merged |
|-------------|
| 431         |

## 用户融合日志表( id_mapping_log ):

在 id_mapping_log 表中，您可以查询用户融合记录，该数据表仅包含被融合用户。

| 列名          | 数值类型 | 业务含义           |
|---------------|----------|--------------------|
| gio_id        | 整数     | 被融合的系统用户ID |
| merged_gio_id | 整数     | 融合后的系统用户ID |
| merged_time   | 日期     | 融合时间           |

时效性：实时

数据延时：秒级

查询示例：

``` sql
-- 查询系统用户(gio_id) 35103 被融合记录

select
    gio_id
    ,merged_gio_id
    ,merged_time
from olap.id_mapping_log
where gio_id = 35103
``` 

| gio_id | merged_gio_id | merged_time             |
|--------|---------------|-------------------------|
| 35103  | 938           | 2021-09-26 05:57:40.595 |

## 维度表( item_view_XXX )：

维度表是拓展分析维度的重要能力，适用于从商品、门店、采购员等角度分析用户行为的场景。每一张维度表，对应一张视图表。

比如，商品维度表，标识符为product，定义的字段如下：

![picture 20](/img/c05c0c42193e218e85485e8ac381b4fa33489fdcf508982ea31a79cc42e3272c_pic_1660121598861_2022-08-10.png)  

该商品维度表的视图表为item_view_product，表结构如下：

![picture 21](/img/4f8d14fdd250431a433348786ab6f2422a8abb338a5324d42b7790b2adcac995_pic_1660121771934_2022-08-10.png)  

该商品维度表的数据字典如下：

| 字段名 | 字段类型 | 含义及实例             |
|--------|---------------|-------------------------|
| item_id  | String  | 商品ID |
| account_id  | String  | 租户ID，由系统提供 |
| itm_prod_name  | String  | 商品名称 |
| itm_prod_level_1  | String  | 商品一级分类 |
| itm_prod_level_2  | String  | 商品二级分类 |
| itm_prod_size  | String  | 商品尺寸 |

## 元数据表：

定义事件、用户属性、用户标签、维度表等的元数据表，可在clickhouse的meta库中查询。

### 埋点事件表（custom_events）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| id  | Int32  | 编号 |
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| is_system  | Int8  | 是否预置。0否 1是 |
| project_id  | Int32  | 项目ID |

### 事件属性表（event_variables）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| id  | Int32  | 编号 |
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| value_type | String | 属性类型。如string | 
| is_system  | Int8  | 是否预置。0否 1是 |
| project_id  | Int32  | 项目ID |

### 事件与属性关系表（event_variable_mapping）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| event_id  | Int32  | 事件ID |
| variable_id  | Int32  | 事件属性ID |
| project_id  | Int32  | 项目ID |

### 维度表（item_models）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| id  | Int32  | 编号 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| is_system  | UInt8  | 是否预置。0否 1是 |
| project_id  | Int32  | 项目ID |

### 维度表字段表（item_variables）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| id  | Int32  | 编号 |
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| value_type  | Int32  | 字段类型。如string |
| is_primary_key  | UInt8  | 是否维度表标识符。0否 1是 |
| is_system  | UInt8  | 是否预置。0否 1是 |
| project_id  | Int32  | 项目ID |

### 事件属性与维度表关系表（event_variable_item_mapping）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| variable_id  | Int32  | 事件属性ID |
| item_id  | Int32  | 维度表记录ID |
| variable_type  | Int32  | 属性类型 |
| project_id  | Int32  | 项目ID |

### 用户属性表（user_variables）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| value_type  | String  | 属性类型。如string、double |
| is_system  | UInt8  | 是否预置。0否 1是 |
| project_id  | Int32  | 项目ID |

### 用户标签表（user_tags）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| value_type  | String  | 标签类型。如string、double |
| project_id  | Int32  | 项目ID |

### 用户分群表（user_segments）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| project_id  | Int32  | 项目ID |
| space_id  | Int32  | 空间ID |

### 用户身份表（user_identities）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| key  | String  | 标识符，如$basic_userId |
| name  | String  | 名称，如登录用户ID |
| description  | String  | 描述 |
| project_id  | Int32  | 项目ID |

### 数据源表（data_sources）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| key  | String  | 标识符 |
| name  | String  | 名称 |
| description  | String  | 描述 |
| project_id  | Int32  | 项目ID |

### 爬虫表（crawler_rules）：

| 字段名 | 数据类型 | 含义及实例             |
|--------|---------------|-------------------------|
| id  | Int32  | 编号 |
| key  | String  | 标识符，如Baiduspider |
| name  | String  | 名称，如百度爬虫 |
| description  | String  | 描述 |
| is_system  | UInt8  | 是否预置。0否 1是 |
| project_id  | Int32  | 项目ID |
