# GrowthBook 简介
## 概览
GrowthBook 是一款 A/B 测试平台，提供从零开始创建实验、分析数据到自动优化整个过程。
代码仓库：https://github.com/growthbook/growthbook
## 特性
* **轻量级** - 提供最简的集成方法，支持任意数据存储
* **无埋点** - 提供了无需埋点即可使用的事件触发和分组功能
* **易于扩展** - 丰富的 SDK 库和 HTTP  API 支持
* **支持多平台** - 提供 web 和移动 SDK 库

## 用户文档
* [文档](https://docs.growthbook.io/)

## 社区
* [Slack 工作区](https://www.growthbook.io/slack)
* [博客](https://blog.growthbook.io/)

## 基础概念
### 功能
GrowthBook 的术语 "Feature" 表示为特定用户而设计的功能切换或变量，例如页面上的按钮颜色、用户界面的动画效果或购物车中的优惠折扣。

### 实验
GrowthBook 的术语 "Experiment" 是指对一个特定功能的配置设置进行测试。这通常涉及 A/B 测试或 MVT，但也可以包含其他类型的测试，如单臂 bandit 测试或多变量分析。

### 分组
GrowthBook 的术语 "Group" 表示一组用户，根据一组条件进行分组，例如所有新客户、特定国家的用户或已购买特定产品的用户。

### 事件
GrowthBook 的术语 "Event" 表示一种触发器，该触发器表示用户已执行某些操作。 例如，当客户购买某产品的特定变体时，我们可能会触发名为 "purchase" 的事件。

### 指标
GrowthBook 的术语 "Metric" 表示需要分析的特定目标。 例如，购买量、销售额或对产品的评价数量。

### 分配
GrowthBook 的术语 "Allocation" 表示分配给一组特定条件或分组的用户百分比。
