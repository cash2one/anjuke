估价频道项目设计
===

项目背景
---

* 站内增加估价频道，为房东引流，通过估价筛选出房东，并引导其卖房。
* 上线城市：[PRD中的37个城市](http://p.corp.anjuke.com/project/detail?id=23267)


功能模块
---

###1. header
* 导航增加‘估价’入口，其URL地址：http://shanghai.anjuke.com/gujia

		app-anjuke/config/navigation.php   增加代码：
				
		'gujia' => array(
		
* 增加估价城市开关config配置

###2. 首页估价入口
* 右侧广告增加‘我要估价’入口

###3. 估价表单初始化
* 生成初始化表单需要的值

###4. 最近被估价的房源
* 显示6套估价房源，不满足6条不显示该模块

		app-biz/classes/biz/ershou/gujia/GujiaBiz.php
		Biz_Ershou_Gujia_GujiaBiz->getGujiaInfoById()

###5. 底部通栏广告
* 广告位新房代码

		线上：http://ifx.aifang.com/s?p=2007&c=11&o=1



###6. 估价结果计算
* 异步计算结果
* 接口地址

		POST：http://shanghai.anjuke.com/v3/ajax/gujia/save/
		参数：
			city_id
			community_id
			community_name
			building_num
			pro_floor
			total_floor
			
	
	
	
###7. 估价结果页面
* 估价表单默认展开
* 我的房产总价

		小区信息：Biz_Community_Comm_CommunityBiz->getCommunityAllInfo()
		小区均价：Ershou_Core_Service_PriceService->getCommPriceMonthlyByCommId()
		估价价格：计算公式见下面
		最新均价：小区均价
		涨／跌幅：小区的涨／跌幅
		
* 均价走势：走势图片
	
		图片地址：/v3/chart/gujia/
	
* 市场热门度：通过小区热度来计算；

		新框架获取小区评分service（热门度：itemd=5）
		Community_Core_Comm_Service_CommunityService－>getNearbyCommunityScore()
	
* 我要卖房：将估价参数传入到卖房页面，使其自动填充卖房页面表单
		
		卖房页面表单参数：
		URL：http://i.anjuke.com/publish?from=gufangjia
		type：POST
		参数说明：http://gitlab.corp.anjuke.com/_broker-docs/i-doc/blob/master/personal/Doc/designDoc/publish_params.md


估价计算公式
---
* 估价计算公式

		$room_num;//几室

数据保存
---
* 数据库：user_prop_db

		
		 CREATE TABLE `property_valuation` (
		
		id说明：为了不让用户认为我们网站的估价数据比较少 ， 故id从5000000开始
				
		
* 接口：

		接口说明地址：http://gitlab.corp.anjuke.com/_broker-docs/i-doc/blob/master/personal/Doc/Api/assess/publish.md


seo
---
* 估价首页

	* title：{城市名称}房价，｛城市名称｝卖房， ｛城市名称｝估房网-｛城市名称｝安居客
	* keyword：｛｛上海｝估房价，｛上海｝卖房
	* description：安居客｛城市名称｝估房价频道，提供｛年份｝年最全、最及时、最精准、最权威的｛城市名称｝房价、｛城市名称｝房价走势图。安居客｛城市名称｝二手房估家频道、实现你快带买房卖房的梦想。

* 估价结果页面
	
	* title：{小区名称}，｛户型｝，{面积}，房价{估算的房价总额}-｛城市名称｝安居客
	* keyword：｛小区名称｝，｛小区名称｝房价
	* description：安居客｛城市名称｝估房价频道，提供｛年份｝年最全、最及时、最精准、最权威的｛小区名称｝房价、｛小区名称｝价走势图。安居客｛城市名称｝二手房估家频道、实现你快带买房卖房的梦想。


服务器部署
---
* 1、url指到新框架：
	* http://{城市}.anjuke.com/gujia/
  	* http://{城市}.anjuke.com/gujia/{ID}.html
* 2、数据表建立:user_prop_db->property_valuation
* 3、估价顶层配置：gujia.php,添加如下配置
		
		$config['save_gujia_data_url'] = 'http:/i.anjuke.com/api/mobile/assess_publish';
