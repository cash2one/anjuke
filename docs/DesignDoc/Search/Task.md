搜索接入eSearch项目
-

*  概述

  房源接入58的搜索技术, 以二手房北京这个城市接入试点,项目设计考虑所有业务的合并查询

* 一期任务

任务 | 责任人 | 时间(天) | 开始 | 结束 | 状态 | 备注
---|---|---|---|---|---|---
与58技术方案讨论         | 刘张健 | - | - | - | Done | - | -
机器采购         	    	| 秦洁 | - | - | - | Todo | 已和秦洁沟通,待采购
DBA支持         | 周枫 | - | - | - | Todo | 初步讨论过
pg环境搜索布部署         | 周乐钦 | - | - | - | Todo | - |
online环境搜索布部署         | 周乐钦 | - | - | - | Todo | - |
搜索框架搭建         | 周乐钦 | - | - | - | Todo | - |
索引数据源表创建         | 刘张健 | 3 | 05-27 | 05-28 | Doing | 待review
数据发布写入Job         | 刘张健 | - | - | - | Todo |写/更新索引源数据表子任务:<br>1.房源更新job接入<br/>2.端口房源推广接入<br/>3.精选推广接入<br/>4.rank要接入?<br/>5.rebuild所有90天内房源
MSS服务（关键词匹配服务)         | 刘张健 | - | - | - | 再讨论 | 问题:<br/>1.用现有词库?
排序打分、滚动策略         | 刘张健 | - | - | - | 再讨论&任务细化 | 此任务要再讨论具体可执行方案<br/>1.58支持?
搜索服务封装web应用         | 刘张健 | - | - | - | 再讨论 |问题:<br/>1.rank均化查询支持<br/>2.新查询语法熟悉<br/>3.搜索后筛选项聚合<br /> 4.区间查询,poi范围查询
灰度发布上线         | 刘张健 | - | - | - | Todo | 子任务:<br/>1.开关配置,回滚原solr搜索<br/>.2监控预警
后期跟进数据         | 刘张健 | - | - | - | Todo | 子任务:<br/>1.转化率<br/>2.列表页速度

* 一期优化任务
   
任务 | 责任人 | 时间(天) | 开始 | 结束 | 状态 | 备注
---|---|---|---|---|---|---
系统稳定性6条监控         | 周乐钦 | - | - | - | Done | - | -
2版本无缝切换         | 周乐钦 | - | - | - | Done | - | -
关键字权重查询         | 秦凯 | - | - | - | Todo | - | -
rank的socre插件时间可配置| 秦凯 | - | - | - | Todo | - | -
mss的处理        | 刘张健 | - | - | - | Todo | - | -
搜索摘要的处理       | 刘张健 | - | - | - | Todo | - | -
测试的自动化回归脚本         | 冯珊珊 | - | - | - | Todo | - | -
验证sort_rank数据效果        | 徐建龙 | - | - | - | Todo | - | -