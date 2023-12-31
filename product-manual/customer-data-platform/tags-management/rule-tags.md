---
id: create-rule-tags
sidebar_position: 3
---

# 创建规则标签

一、在 **客户数据平台 > 标签管理** 中点击 **新建用户标签** 进入用户标签创建弹窗

![图 7](/img/2a0c3cea73a1ebdaf09346932caa95924b90e3f9909d9dd2c8a9b1cf321d2210.png)  

二、选择 **标签类型** 并填写标签基本信息

| 项       | 是否必填 | 说明                                       | 限制条件                                                                          |
| -------- | -------- | ------------------------------------------ | --------------------------------------------------------------------------------- |
| 名称     | 是       | 用户标签名称                               | 名称唯一，不可重复<br/>最大输入 30 个字符                                         |
| 标识符   | 是       | 用户标签标识符<br/>可用于数据库和 API 查询 | 名称唯一，不可重复<br/>最大输入 100 个字符<br/>仅允许大小写英文、数字、以及下划线 |
| 描述     | 否       | 用户标签的业务意义描述                     | 最大输入 150 个字符                                                               |
| 所属分类 | 否       | 选择自定义的用户标签分类                   | 如不选择则为未分类                                                                |

![](/img/用户标签-基本信息.png)

三、点击 **下一步** 开始定义标签规则

## 3.1 基础指标值

> 把事件的统计结果作为标签值，对用户打标。[点击查看说明](../user-tags#基础指标值)

### 控件说明

| 项              | 说明                 | 限制条件                               |
| --------------- | -------------------- | -------------------------------------- |
| 时间选择器      | 选择打标事件发生时间 | 无                                     |
| 事件选择器      | 选择打标事件         | 支持埋点事件和虚拟事件                 |
| 维度+度量选择器 | 选择打标事件计算逻辑 | 支持选择字符串、整数、小数类型事件属性 |
| 过滤选择器      | 选择事件过滤条件     | 支持选择事件属性和用户属性             |

![](/img/用户标签-基础指标值.png)

#### 维度+度量选择器

系统根据不同数值类型的事件属性，支持以下度量方式：

| 维度               | 总和(度量) | 平均值(度量) | 中位数(度量) | 累计占比(度量) | 去重数(度量) |
| ------------------ | ---------- | ------------ | ------------ | -------------- | ------------ |
| 次数               | ✓          | -            | -            | ✓              | -            |
| 总天数             | -          | -            | -            | -              | -            |
| 整数、小数事件属性 | ✓          | ✓            | ✓            | ✓              | -            |
| 字符串事件属性     | -          | -            | -              | -              | ✓            |

### 统计逻辑

#### 例 1: 过去 30 天，直接访问次数总和

业务定义：使用过去 30 天，每个用户直接访问的次数作为标签值，对用户进行打标

![](/img/用户标签-基础指标值-例1.png)

```sql
select
    gio_id          as gio_id
    ,count(1)       as tag_value                            -- 事件次数
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
    and event_key = '$visit'                                -- 事件标识符
    and $referrer_type is null                              -- 事件过滤
group by gio_id
```

#### 例 2: 过去 30 天，总访问天数

业务定义：使用过去 30 天，每个用户访问的总天数作为标签值，对用户进行打标

![](/img/用户标签-基础指标值-例2.png)

```sql
select
    gio_id                      as gio_id
    ,count( distinct dt )       as tag_value                -- 事件总天数
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
    and event_key = '$visit'                                -- 事件标识符
group by gio_id
```

#### 例 3: 过去 30 天，订单支付金额总和

业务定义：使用过去 30 天，每个用户订单支付总金额作为标签值，对用户进行打标

![](/img/用户标签-基础指标值-例3.png)

```sql
select
    gio_id                      as gio_id
    ,sum( var_payamount )       as tag_value                -- 订单支付总金额
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
    and event_key = 'payment'                               -- 事件标识符
    and var_payamount is not null
group by gio_id
```

#### 例 4: 过去 30 天，订单支付金额平均值

业务定义：使用过去 30 天，每个用户订单支付金额的平均值作为标签值，对用户进行打标

> 注 1: 仅统计有效订单(金额有值)的订单金额平均值
> 注 2: 计算结果保留小数点后 2 位有效数字

![](/img/用户标签-基础指标值-例4.png)

```sql
select
    gio_id                                  as gio_id
    ,round( avg( var_payamount ) , 2 )      as tag_value    -- 金额平均值
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
    and event_key = 'payment'                               -- 事件标识符
    and var_payamount is not null
group by gio_id
```

#### 例 5: 过去 30 天，订单支付金额累计占比

业务定义：使用过去 30 天，每个用户 3c 类产品支付金额占总支付金额的占比作为标签值，对用户进行打标

> 注 1: 仅统计有效订单(金额有值)
> 注 2: 计算结果保留小数点后 2 位有效数字

![](/img/用户标签-基础指标值-例5.png)

```sql
select
    gio_id                                  as gio_id
    ,round( sum( if( var_product_layer1 = '3c' , var_payamount , 0 ) ) /
            sum( var_payamount ) , 2 )      as tag_value    -- 金额累计占比
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
    and event_key = 'payment'                               -- 事件标识符
    and var_payamount is not null
group by gio_id
```

#### 例 6: 过去 30 天，订单支付优惠券类型去重数

业务定义：使用过去 30 天，每个用户订单支付中使用优惠券类型去重个数作为标签值，对用户进行打标

> 注：标签计算结果中，
> 过去 30 天，未发生订单支付事件的用户标签值为 null
> 过去 30 天，发生订单支付但未使用优惠券的用户标签值为 0
> 过去 30 天，发生订单支付且使用优惠券的用户标签值为 1,2,3...

![](/img/用户标签-基础指标值-例6.png)

```sql
select
    gio_id                                  as gio_id
    ,count( distinct var_coupon_type )      as tag_value    -- 优惠券类型去重数
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
    and event_key = 'payment'                               -- 事件标识符
group by gio_id
```

#### 例 7: 过去 180 天，浏览房产价格的中位数

业务定义：使用过去 180 天，每个用户浏览房产价格的中位数作为标签值，对用户进行打标

> 注：标签计算结果中，
> 过去 180 天，未浏览房产的用户标签值为 null
> 过去 180 天，浏览房产的用户标签值为其浏览价格的中位数

![](/img/用户标签-基础指标值-例7.png)

```sql
select
    gio_id                                  			as gio_id
    ,quantileExactInclusive(0.5)(var_property_price)    as tag_value    -- 房产价格中位数
from olap.event
where account_id = 'bc675c65b3b0290e'                       -- 项目ID
    and dateDiff( 'day' , dt , today()) between 1 and 180    -- 时间筛选
    and event_key = 'property_view'                               -- 事件标识符
	and var_property_price is not null
group by gio_id
```

## 3.2 最大值/最小值的事件属性

> 按事件属性分组后，对发生次数或整数、小数类型事件属性求和最多或最少的分组属性值作为标签值，对用户打标。[点击查看说明](../user-tags#最大值最小值的事件属性)

### 控件说明

| 项             | 说明                   | 限制条件                                              |
| -------------- | ---------------------- | ----------------------------------------------------- |
| 时间选择器     | 选择打标事件发生时间   | 无                                                    |
| 事件选择器     | 选择打标事件           | 支持埋点事件和虚拟事件                                |
| 排序维度选择器 | 选择计算维度和排序逻辑 | 支持整数、小数类型事件属性<br/>支持最多和最少排序逻辑 |
| 分组维度选择器 | 选择分组维度           | 支持字符串类型事件属性                                |
| 过滤选择器     | 选择事件过滤条件       | 支持选择事件属性和用户属性                            |

![](/img/用户标签-最大值最小值.png)

### 统计逻辑

#### 例 1：过去 30 天，购买金额最多的一级品类

业务定义：将过去 30 天每个用户订单支付金额总和按商品一级品类分组，使用支付金额最多的分组品类名称作为标签值，对用户进行打标

> 注 1：仅从商品一级品类名称(分组维度)非空值中进行排序打标
> 注 2：当多种一级品类(分组维度)支付总额并列最多时，随机返回其中某个一级品类名称

![](/img/用户标签-最大值最小值-例1.png)

```sql
select
	gio_id 								as gio_id
	,groupArray(1)(group_attribute)[1]	as tag_value
from
(
	select
	    gio_id                          as gio_id
	    ,var_product_layer1 			as group_attribute  	-- 分组维度
	    ,sum( var_payamount )      		as order_attribute    	-- 排序维度
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
	    and event_key = 'payment'                               -- 事件标识符
        and var_product_layer1 is not null
	group by gio_id
		,var_product_layer1
	order by gio_id,order_attribute desc
)
group by gio_id
```

## 3.3 首次/末次的事件属性

> 将事件第一次或最后一次发生的字符串类型事件属性作为标签值，对用户打标。[点击查看说明](../user-tags#首次末次的事件属性)

### 控件说明

| 项         | 说明                 | 限制条件                           |
| ---------- | -------------------- | ---------------------------------- |
| 时间选择器 | 选择打标事件发生时间 | 无                                 |
| 事件选择器 | 选择打标事件         | 支持埋点事件和虚拟事件             |
| 逻辑选择器 | 选择计算逻辑         | 支持首次发生和末次发生             |
| 维度选择器 | 选择打标维度         | 支持距今天数、日期和字符串事件属性 |
| 过滤选择器 | 选择事件过滤条件     | 支持选择事件属性和用户属性         |

![](/img/用户标签-最初最终.png)

### 编辑限制

**首次/末次的事件属性** 支持将某个事件的发生时距今天数(小数)、发生时的具体日期(日期)和发生时的事件属性(字符串)作为标签值，对用户打标。且已保存标签支持在分析工具、群体画像等应用中作为维度过滤和维度拆解使用。为保证标签在应用中平稳使用，标签编辑时禁止修改标签的数值类型。因此编辑标签时具有以下限制：

| 当前选择 | 修改-距今天数 | 修改-日期 | 修改-事件属性 |
| -------- | ------------- | --------- | ------------- |
| 距今天数 | ✓             | -         | -             |
| 日期     | -             | ✓         | -             |
| 事件属性 | -             | -         | ✓             |

### 统计逻辑

#### 例 1：过去 30 天，首次订单支付距今天数

业务定义：使用过去 30 天，每个用户首次发生订单支付距今天数作为标签值，对用户进行打标

![](/img/用户标签-首次末次-例1.png)

```sql
select
	gio_id 									as gio_id
	,groupArray(1)(target_atttribute)[1]	as tag_value
from
(
	select
	    gio_id                          	as gio_id
	    ,dt 								as order_attribute
	    ,dateDiff( 'day' , dt , today())	as target_atttribute
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
	    and event_key = 'payment'                               -- 事件标识符
	order by gio_id,dt
)
group by gio_id
```

#### 例 2：过去 30 天，末次订单支付日期

业务定义：使用过去 30 天，每个用户末次发生订单支付的具体日期作为标签值，对用户进行打标

![](/img/用户标签-首次末次-例2.png)

```sql
select
	gio_id 									as gio_id
	,groupArray(1)(target_atttribute)[1]	as tag_value
from
(
	select
	    gio_id                          	as gio_id
	    ,dt 								as order_attribute
	    ,dt	                                as target_atttribute
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
	    and event_key = 'payment'                               -- 事件标识符
	order by gio_id,dt desc
)
group by gio_id
```

#### 例 3：过去 30 天，首次使用的优惠券类型

业务定义：使用过去 30 天，每个用户发生订单支付时，首次使用的优惠券类型作为标签值，对用户进行打标

> 注 1：仅从优惠券类型非空值中进行排序打标，即有使用优惠券的用户
> 注 2：以天粒度排序，如果一天内用户使用多个优惠券，随机返回其中某个优惠券名称

![](/img/用户标签-首次末次-例3.png)

```sql
select
	gio_id 									as gio_id
	,groupArray(1)(target_atttribute)[1]	as tag_value
from
(
	select
	    gio_id                          	as gio_id
	    ,dt 								as order_attribute
	    ,var_conpon_type	                as target_atttribute
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
	    and event_key = 'payment'                               -- 事件标识符
        and var_conpon_type is not null
	order by gio_id,dt
)
group by gio_id
```

## 3.4 列表类的事件属性

> 将事件的全部事件属性列表作为标签值，对用户打标，常用于偏好类标签。[点击查看说明](../user-tags#列表类的事件属性)

### 控件说明

| 项         | 说明                 | 限制条件                   |
| ---------- | -------------------- | -------------------------- |
| 时间选择器 | 选择打标事件发生时间 | 无                         |
| 事件选择器 | 选择打标事件         | 支持埋点事件和虚拟事件     |
| 维度选择器 | 选择打标维度         | 支持字符串事件属性         |
| 过滤选择器 | 选择事件过滤条件     | 支持选择事件属性和用户属性 |

![](/img/用户标签-列表.png)

### 统计逻辑

#### 例 1：过去 30 天，使用的全部优惠券类型偏好

业务定义：使用过去 30 天，每个用户使用的全部优惠券类型(去重)作为标签值，对用户进行打标

> 注 1：仅筛选使用优惠券的订单
> 注 2：优惠券类型去重

![](/img/用户标签-列表-例1.png)

```sql
select
	gio_id 									as gio_id
	,groupArray(target_atttribute)	        as tag_value
from
(
	select
	    gio_id                          	as gio_id
	    ,var_conpon_type	                as target_atttribute
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
	    and event_key = 'payment'                               -- 事件标识符
        and var_conpon_type is not null
	group by gio_id,var_conpon_type
)
group by gio_id
```


## 3.5 计算标签
> 计算标签支持将数值型的用户属性及规则标签，通过四则运算生成新标签。[点击查看说明](../user-tags#计算标签)

### 控件说明

| 项         | 说明                 | 限制条件                               |
| ---------- | -------------------- | -------------------------------------- |
| 属性选择器 | 选择用户特征         | 支持选择整数型和小数型的用户属性和用户标签(除分层标签、用户评分标签、SQL标签) |
| 计算公式 | 标签的四则运算公式，引用运算指标中各运算项的英文字母代号做计算 | 无                                     |
![图 9](/img/210592d98e84b1bc174ee2cb434ba61a7a9915b922b73d7c6a500c1073827690.png)  

### 统计逻辑

> 如果一个用户同时满足第 1 分层和第 2 分层的规则，优先被计算在第 1 分层里，第 2 分层不再计算

#### 例 1：近30天用户客单价

业务定义：

- **近30天用户笔单价**=近30天用户购买金额÷近30天用户购买次数
![图 8](/img/f88c6407bc23f65429d41699fc96f6c5cd3c854a87d54dd4214f17e42b3b118c.png)  


```sql
SELECT  gio_id
       ,tag_j30tgmje / tag_j30tgmcs AS tag_value
FROM user_view
WHERE tag_j30tgmje IS NOT NULL
AND tag_j30tgmcs IS NOT NUL
```

## 3.6 用户评分标签
> 用户评分标签支持对用户属性/用户标签/用户身份信息分别进行评分加总生成新标签。[点击查看说明](../user-tags#用户评分标签)

### 控件说明
用户评分标签支持根据 **用户行为** 和 **用户属性** 自定义评分规则。

#### 用户行为 - 做过

| 项              | 说明                       | 限制条件                                                |
| --------------- | -------------------------- | ------------------------------------------------------- |
| 时间选择器      | 选择事件发生时间           | 无                                                      |
| 事件选择器      | 选择事件                   | 支持埋点事件和虚拟事件                                  |
| 维度+度量选择器 | 选择事件计算逻辑           | 支持选择次数总和<br/>支持选择整数、小数类型事件属性总和 |
| 行为条件选择器  | 选择事件计算结果的筛选条件 | 无                                                      |
| 过滤选择器      | 选择事件过滤条件           | 支持选择事件属性和用户属性                              |
| 评分器      | 设定当前条件的评分规则           | 支持加分、减分，且必须输入正整数                              |
![图 10](/img/c9e767efbebed428c98c00c9a2097b7c06a3eac9f67a88094d9a330b7e0011a5.png)  


#### 用户行为 - 未做过

| 项         | 说明             | 限制条件                   |
| ---------- | ---------------- | -------------------------- |
| 时间选择器 | 选择事件发生时间 | 无                         |
| 事件选择器 | 选择事件         | 支持埋点事件和虚拟事件     |
| 过滤选择器 | 选择事件过滤条件 | 支持选择事件属性和用户属性 |
| 评分器      | 设定当前条件的评分规则           | 支持加分、减分，且必须输入正整数                              |
![图 12](/img/a8fd65655945a3c406755b44bd25f7b183666dd1e2a4433d7b8cfe1a01be3810.png)  
#### 用户属性

| 项         | 说明                 | 限制条件                               |
| ---------- | -------------------- | -------------------------------------- |
| 属性选择器 | 选择用户特征         | 支持选择用户身份、用户属性和用户标签(除分层标签) |
| 过滤选择器 | 选择用户特征过滤条件 | 无                                     |
| 评分器      | 设定当前条件的评分规则           | 支持加分、减分，且必须输入正整数                              |
![图 13](/img/15ae1d13b11e2f995cde8fea0e3606166b0b13ebcc3b259514332aec14ef4e66.png)  


### 统计逻辑

> 分别计算每个用户评分规则进行评分赋值，然后进行求和加总。评分器选择加分时，该评分项为正数；选择减分时，该评分项为负数。

#### 例 1：用户活跃分

业务定义：

- **近30天浏览次数1次以上** 加10分
- **近30天加购次数1次以上** 加50分
- **近30天看过直播** 加100分
![图 14](/img/bbd67cb9eb339c55913ddc821267dd6de6b612846ccb0c17446c3bfcb090bc81.png)  
```sql
SELECT  gio_id
       ,SUM(CASE WHEN flag_A1 = 1 THEN 10 WHEN flag_A2 = 1 THEN 50 WHEN flag_A3 = 1 THEN 100 END) AS tag_value
FROM
(
	SELECT  gio_id
	       ,MAX(flag = 'A1') AS flag_A1
	       ,MAX(flag = 'A2') AS flag_A2
	       ,MAX(flag = 'A3') AS flag_A3
	FROM
	(
		SELECT  gio_id AS gio_id
		       ,'A1'   AS flag
		FROM
		(
			SELECT  gio_id   AS gio_id
			       ,COUNT(1) AS term0
			FROM event_view
			WHERE ((event_key = '$page'))
			AND event_time >= '2022-08-27'
			AND event_time < '2022-09-26'
			GROUP BY  gio_id
			HAVING term0 >= 1.0
		) A1
		UNION ALL
		SELECT  gio_id AS gio_id
		       ,'A2'   AS flag
		FROM
		(
			SELECT  gio_id   AS gio_id
			       ,COUNT(1) AS term0
			FROM event_view
			WHERE ((event_key = 'add_to_cart'))
			AND event_time >= '2022-08-27'
			AND event_time < '2022-09-26'
			GROUP BY  gio_id
			HAVING term0 >= 1.0
		) A2
		UNION ALL
		SELECT  gio_id AS gio_id
		       ,'A3'   AS flag
		FROM
		(
			SELECT  gio_id   AS gio_id
			       ,COUNT(1) AS term0
			FROM event_view
			WHERE ((event_key = 'E_JoinLive'))
			AND event_time >= '2022-09-19'
			AND event_time < '2022-09-26'
			GROUP BY  gio_id
			HAVING term0 >= 1.0
		) A3
	) tbl
	GROUP BY  gio_id
)
GROUP BY  gio_id;
```
### 例 2：用户价值分

业务定义：

- **关注公众号** 加100分
- **取关公众号** 减100分
- **用户为女性** 加50分
- **近30天购买金额大于等于300** 加300分
![图 15](/img/b2bbc597d7b18014658c2e2b2bcd7ed6b868b98b9450c84e9b7eb664bb254997.png)  
```sql
SELECT  gio_id
       ,SUM(CASE WHEN flag_A2 = 1 THEN -100 WHEN flag_A1 = 1 THEN 100 WHEN flag_A3 = 1 THEN 50 WHEN flag_A4 = 1 THEN 300 END) AS tag_value
FROM
(
	SELECT  gio_id
	       ,MAX(flag = 'A2') AS flag_A2
	       ,MAX(flag = 'A1') AS flag_A1
	       ,MAX(flag = 'A3') AS flag_A3
	       ,MAX(flag = 'A4') AS flag_A4
	FROM
	(
		SELECT  gio_id AS gio_id
		       ,'A2'   AS flag
		FROM
		(
			SELECT  gio_id   AS gio_id
			       ,COUNT(1) AS term0
			FROM event_view
			WHERE ((event_key = 'LEGO_focus'))
			AND event_time >= '2020-09-01'
			AND event_time < '2022-09-27'
			GROUP BY  gio_id
			HAVING term0 >= 1.0
		) A2
		UNION ALL
		SELECT  gio_id AS gio_id
		       ,'A1'   AS flag
		FROM
		(
			SELECT  gio_id   AS gio_id
			       ,COUNT(1) AS term0
			FROM event_view
			WHERE ((event_key = 'LEGO_scancode'))
			AND event_time >= '2020-09-01'
			AND event_time < '2022-09-27'
			GROUP BY  gio_id
			HAVING term0 >= 1.0
		) A1
		UNION ALL
		SELECT  term0 AS gio_id
		       ,'A3'  AS flag
		FROM
		(
			SELECT  gio_id AS term0
			FROM user_view
			WHERE ((usr_$basic_gender = 'Female'))
		) A3
		UNION ALL
		SELECT  term0 AS gio_id
		       ,'A4'  AS flag
		FROM
		(
			SELECT  gio_id AS term0
			FROM user_view
			WHERE ((tag_j30tgmje >= 300.0))
		) A4
	) tbl
	GROUP BY  gio_id
)
GROUP BY  gio_id;
```

## 3.7 分层标签

> 根据自定义规则对用户进行分层打标，常用于生命周期、RFM、线索分层、用户分层等业务模型标签的构建。[点击查看说明](../user-tags#分层标签)

### 控件说明

分层标签支持根据 **用户行为** 和 **用户属性** 自定义用户分层。

> 注 1：分层名称不能为空，且不同分层名称不能重复
> 注 2：最多支持 10 个用户分层
> 注 3：每个分层最多支持 10 条筛选规则

#### 用户行为 - 做过

| 项              | 说明                       | 限制条件                                                |
| --------------- | -------------------------- | ------------------------------------------------------- |
| 时间选择器      | 选择事件发生时间           | 无                                                      |
| 事件选择器      | 选择事件                   | 支持埋点事件和虚拟事件                                  |
| 维度+度量选择器 | 选择事件计算逻辑           | 支持选择次数总和<br/>支持选择整数、小数类型事件属性总和 |
| 行为条件选择器  | 选择事件计算结果的筛选条件 | 无                                                      |
| 过滤选择器      | 选择事件过滤条件           | 支持选择事件属性和用户属性                              |

![](/img/用户标签-分层标签-用户行为.png)

#### 用户行为 - 未做过

| 项         | 说明             | 限制条件                   |
| ---------- | ---------------- | -------------------------- |
| 时间选择器 | 选择事件发生时间 | 无                         |
| 事件选择器 | 选择事件         | 支持埋点事件和虚拟事件     |
| 过滤选择器 | 选择事件过滤条件 | 支持选择事件属性和用户属性 |

![](/img/用户标签-分层标签-用户行为-未做过.png)

#### 用户属性

| 项         | 说明                 | 限制条件                               |
| ---------- | -------------------- | -------------------------------------- |
| 属性选择器 | 选择用户特征         | 支持选择用户身份、用户属性和用户标签(除分层标签) |
| 过滤选择器 | 选择用户特征过滤条件 | 无                                     |

![](/img/用户标签-分层标签-用户属性.png)

### 编辑限制

**分层标签** 为防止计算循环依赖，分层标签暂不支持引用SQL标签


### 统计逻辑

> 如果一个用户同时满足第 1 分层和第 2 分层的规则，优先被计算在第 1 分层里，第 2 分层不再计算

#### 例 1：用户活跃度分层

业务定义：

- **高活跃**：过去 30 天，活跃天数大于 3
- **中活跃**：过去 30 天，活跃天数大于 1 且小于等于 3
- **低活跃**；过去 30 天，活跃天数等于 1
- **流失**：过去 30 天，未活跃（即所有用户中，未发生任意行为的用户）

![](/img/用户标签-分层标签-例1.png)

```sql
select
	u.gio_id 								as gio_id
	,case when e.activity_days > 3 then '高活跃'
		when e.activity_days > 1 then '中活跃'
		when e.activity_days = 1 then '低活跃'
		else '流失'
	end 									as tag_value
from
(
	select
		gio_id 								as gio_id
	from olap.user
	where usr_$first_day is not null
) u  								-- 全量用户
left join
(
	select
	    gio_id                          	as gio_id
	    ,count( distinct dt )				as activity_days
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 30    -- 时间筛选
	    and event_key = '$anyevent'                             -- 事件标识符
	group by gio_id
) e 								-- 活跃天数
	on u.gio_id = e.gio_id
```

#### 例 2：用户价值分层

业务定义：

- **高价值**：
  用户行为：过去 180 天，订单支付金额总和大于 1 万元
  且
  用户属性：最后一次订单支付时间在过去 30 天之内
- **中价值**：
  用户行为：过去 180 天，订单支付金额总和大于 6 千元
  且
  (
  用户属性：交易频次大于 5
  或
  用户属性：会员等级为付费会员
  )
- **低价值**：
  用户行为：过去 180 天，订单支付金额总和大于 3 千元

> 仅对符合分层定义的用户打标，其余用户标签值为 null

![](/img/用户标签-分层标签-例2.png)

> sql 示例仅为业务逻辑示意，当使用 sql 标签或 zeppelin 查询时，需要进行 sql 查询性能优化

```sql
select
	u.gio_id 								as gio_id
	,case when e.pay_sum > 10000
			and dateDiff( 'day' , u.tag_last_pay_date , today() ) between 0 and 29
				then '高价值'
		when e.ay_sum > 6000
			and ( y.tag_payment_freq > 5
				or u.usr_membership = '付费会员')
				then '中价值'
		when e.pay_sum > 300
				then '低价值'
	end 									as tag_value
from
(
	select
		gio_id
		,tag_last_pay_date
		,tag_payment_freq
		,usr_membership
	from olap.user
	where usr_$first_day is not null
) u
left join
(
	select
	    gio_id                          	as gio_id
	    ,sum( var_payamount )				as pay_sum
	from olap.event
	where account_id = 'bc675c65b3b0290e'                       -- 项目ID
	    and dateDiff( 'day' , dt , today()) between 1 and 180   -- 时间筛选
	    and event_key = 'payment'                             	-- 事件标识符
	group by gio_id
) e
having tag_value is not null
```
