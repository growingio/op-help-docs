---
id: product-analysis
sidebar_position: 1
---

import Tabs from '@theme/Tabs';
import TabItem from '@theme/TabItem';

# 增长分析

## 简介[](#jian-jie)

GrowingIO 增长分析帮助您研究用户的行为，以便您做出更好的产品改进、运营优化等决策。本文针对增长分析的核心概念和通用能力做介绍。

## 名词解释[](#ming-ci-jie-shi)

### 事件[](#shi-jian)

事件是记录用户的交互行为的一种通用表达方式，可以描述用户在任何一个时间点的任意行为。事件具有名称（如：“购买“），时间戳、用户ID和一组属性（如：商品：“iPhone“，价格：“8000“）。

### 属性[](#shu-xing)

属性用于描述用户、事件或维度表的相关信息（详细介绍参见[属性模型](https://growingio.gitbook.io/op/v/14.7/getting-started/basic-concept/property-model)），在增长分析里可应用于定义指标、条件过滤、属性拆解等场景。

### 指标[](#zhi-biao)

指标用具体的数值来表示事件、用户或属性，可用于追踪和监控业务关键环节的数据表现。GrowingIO 把一些常见指标做为预定义指标，如：访问量、跳出率等。您可以选取任意事件和度量方式作为指标来进行相关的分析，例如：衡量订单均价可选取“下单成功“事件的“订单金额“ - “平均值“来定义。

在 GrowingIO，您可以用“事件“及其度量方式来定义“指标“，用“属性“进行过滤或拆解分析。

## 视图介绍[](#shi-tu-jie-shao)

<Tabs>
<TabItem value="line" label="线图" default>

线图可以用于观察一个或多个数据指标连续变化的趋势，也可以根据需要与之前周期进行同比数据分析。线图的横轴是系统默认的时间，纵轴是指标。点击选择具体指标，该指标的数据会依次添加到右侧的线图中。

![线图](/img/assets-M2qbZInaXgdm8kkNosp-M3e2agZWx8gCah9sm-L-M3e39599Wb2pD-csCfhimage.png)

您可以选择一个或多个指标，这些指标都会在同一个坐标轴中呈现，所以建议同张图表中的指标的数量级是类似的。

例如页面浏览量和访问用户量放在一起比较合适；按钮的浏览量和点击量要视情况而定，数据相差几十倍就不太合适；我们有两种数量级，量和百分比，浏览量和点击率，放在同一个坐标轴中就完全不合适。

当您选择显示今天、昨天以及最近 7 天之内的数据时，我们可以提供小时粒度的数据（周期对比曲线图除外）； 如果选择最近 7 天之外的日期时，无法展示小时颗粒度的数据，具体的颗粒度根据您选择的时间范围可调整为：天/周/月，便于您根据场景追踪具体指标随时间的变化。

**为什么会出现虚线？**

可能会有两种情况，一种是代表不完整周期，比如周期是月，但是这个月还没有满 30 天，这时给出的本月数据是虚线；另一种情况是当前时间粒度下没有数据。

</TabItem>
<TabItem value="vbar" label="纵向柱图">

纵向柱图主要用于分析和对比各类别之间的数值大小 ，其中横轴表示需要对比的分类维度，纵轴表示相应的指标数值。

您可以通过纵向柱图分析一个或多个指标在特定维度的分类表现。例如，产品经理和运营人员可以使用纵向柱图分析过去一段时间内不同访问来源过来的流量表现。

![纵向柱图](/img/assets-M2qbZInaXgdm8kkNosp-M3e2agZWx8gCah9sm-L-M3e3xu0s8TCna2UgGifimage.png)

</TabItem>
<TabItem value="hbar" label="横向柱图">

横向柱图是一种频数图，主要用于观察某个指标在某个维度上的分布。根据业务需求对指标按照一定维度拆分，对比不同组别的频数，便于分清轻重缓急， 您可以选择指标以及维度，进行时间范围调整和维度过滤。

![横向柱图](/img/assets-M2qbZInaXgdm8kkNosp-M3e2agZWx8gCah9sm-L-M3e4Q6aFn54qJqMhs0pimage.png)

横向柱图清晰展示了用户在不同类别上的频数，并且按照数量从大到小进行排序。在资源有限的情况下产品可以先适配 Chrome 浏览器以提升绝大部分用户的体验。 常用来细分的维度，如浏览器，操作系统，城市，App 版本，设备型号，广告来源等。

</TabItem>
<TabItem value="table" label="表格">

表格是信息最密集的呈现方式，可以同时分析多指标和多维度的数据，您可以选择指标和维度，然后设置时间范围和展示粒度，可以进行维度过滤。

![表格](/img/assets-M2qbZInaXgdm8kkNosp-M3e2agZWx8gCah9sm-L-M3e5ASC1Cd-gYlxBz17image.png)

相较其他图表形式，表格不那么容易看出变化趋势，但是能更快地得到具体数值。对于核心 KPI 或您关心的指标，快速进行多维度拆解，灵活性高。图表中最多展示 100 条信息。

在对多个目标用户群进行多事件指标、多维度分析时，表格将对事件指标进行拆分排序，并将各事件指标聚合在对应目标用户群下。

如未勾选时间维度，则按第一个目标用户群的第一个指标进行排序；如勾选时间维度，则默认按时间维度进行排序。

</TabItem>
<TabItem value="bubble" label="气泡图">

气泡图主要用于分析多个事件在一个维度上的关系，比如油耗、速度、价格和不同的车型之间的关系。您可以分别设置 X 轴、Y 轴和大小的具体事件。

</TabItem>
<TabItem value="pie" label="环形图">

当维度值的个数比较少的时候，您可以选择环形图进行占比分析。我们默认为您标识出 Top5 维度值的占比，以便您查看重要的维度值。

如果您要分析的维度值较多、或是数值之间差异较小，建议使用横向柱图来呈现，原因是环形图不容易区分出来细小的差异。

![环形图](/img/assets-M2qbZInaXgdm8kkNosp-MVQkwJRs6eFaHkG2bNA-MVQlvRAPwrj6AjvUTadimage.png)

注意：总计指当前维度条数（前20）下的维度值的算数之和。暂不支持展示多个目标用户。

</TabItem>
<TabItem value="kpi" label="大字图">

大字图用于展示单个指标的数值，追踪同环比变化情况。您可以通过“KPI 分析“ 制作大字图，并添加到看板进行监控。如果您监测的指标具有目标值，可设置后追踪目标的完成率。

![大字图](/img/assets-M2qbZInaXgdm8kkNosp-MjXLGqr2pfIe_SXnHUY-MjXLz-9DyMSeZ6CZJ1Zimage.png)​

</TabItem>
</Tabs>
