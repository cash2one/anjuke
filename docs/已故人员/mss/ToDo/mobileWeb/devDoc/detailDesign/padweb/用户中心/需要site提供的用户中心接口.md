###用户中心`注册/登录`需要site提供的用户中心接口

###1.提供给普通用户的登录接口

普通用户登录后可以为用户同步未登录时的房源收藏到用户中心。

pad提供的参数，pad的参数将过滤非法字符/html标签和空格后以明文post给接口

**参数** | **类型** | 说明
--- | --- | ---
account | string | 账户
password | string | 密码


希望从接口获得的参数

**参数** | **说明**
--- | ---
true | 注册成功
false | 注册失败

####2.判断用户是否已经注册

pad提供的参数

**参数** | **类型** | 说明
--- | --- | ---
account | string | 用户账户
type | string | 用户账户类型，详细参见用户账户类型表


用户账户类型表

**值** | **说明**
--- | ---
email | 邮箱
mobile | 手机


希望从接口获得的参数

**参数** | **说明**
--- | ---
code | 结果代码
msg | code描述


账户验证结果code对照表

**值** | **说明**
--- | ---
0 | 可注册
1 | 手机已经被注册
2 | 邮箱已经被注册
3 | 账户格式不正确
4 | 其他


####3.判断密码是否符合网站要求

pad提供的参数

**参数** | **类型** | 说明
--- | --- | ---
password | string | 密码


希望从接口获得的参数

**参数** | **说明**
--- | ---
true | 密码可用
false | 密码格式不正确


####4.提供给普通用户的注册接口

pad提供的参数，pad的参数将过滤非法字符/html标签和空格后以明文post给接口

**参数** | **类型** | 说明
--- | --- | ---
account | string | 账户
password | string | 密码


希望从接口获得的参数

**参数** | **说明**
--- | ---
true | 注册成功
false | 注册失败



