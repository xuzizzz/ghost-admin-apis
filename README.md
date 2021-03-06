# Ghost 的 api 的整理

前缀一般为 `/ghost/api/v0.1/`

请求分为两种，一种是公开的，一种是私有的，公开的接口可以直接访问，但是私有的接口需要添加一些表示登陆的凭着，登陆请求头如下

##### 登陆头部校验

|字段|说明|例子|
|-|-|-|
|authorization|登陆校验请求头，使用`Bearer <token>`的组合，token是使用登陆请求之后的返回值accesstoken|`Bearer AUll3lBzmZK89bJqtJuQEA17K1LEgKnVNVJlFcuyE238A57z5Qsy4eL6wTSerrVUkqKsSOv2fSc4BGrcCdR8aLT35m4yC23tTxrkzM55BV73MWKp3KGNzD46BSSmsd7Kt1i9dNg1IJcuBZhDl8sj4qdS13COpSF5EImhR9kgSeqJneeVMZhQvBDgdTPnZVwaJtl3ADXvEBaxlVPnErgIZRdQepsE8sY7F6YdNcm6ZqWsnOGXelYOoWal5yeifNn`|

### 登陆请求

请求路径: `/authentication/token`</br>
请求类型: post</br>
头部:

|字段|说明|🌰|
|-|-|-|
|Content-Type|请求体的类型|`application/json`|
|origin|请求来源，同源策略，不同源ghost会返回错误|https://xxx.xxxx.xxxx|

请求体(body):

|字段|说明|例子|
|-|-|-|
|grant_type|鉴权类型，登陆一般为`password`|password|
|username|用户登录名|guoshencheng1@gmail.com|
|password|用户登陆的密码|123456|
|client_id|数据库中存储的，可以从数据库中查看|xxxxx|
|client_secret|数据库中存储的，可以从数据库中查看|xxxxx|

### 发送重置密码邮件

请求路径: `/authentication/passwordreset`</br>
请求类型: post</br>

请求体(body):

请求体被放置在`passwordreset`中，passwordreset是一个数组，但是数组中只有一个元素，元素的字段如下

|字段|说明|例子|
|-|-|-|
|email|需要重置密码的邮箱|guoshencheng1@gmail.com|


### 重置密码

请求路径: `/authentication/passwordreset`</br>
请求类型: put</br>

请求体(body):

请求体被放置在`passwordreset`中，passwordreset是一个数组，但是数组中只有一个元素，元素的字段如下

|字段|说明|例子|
|-|-|-|
|token|从邮箱点击链接之后会带上一个token，需要在修改密码的时候带上这个token|xxxxxxxxxxxxxxxx|
|newPassword|新的密码|123456|
|ne2Password|新的密码的第二次输入值|123456|


### 接受邀请

请求路径: `/authentication/invitation`</br>
请求类型: post</br>

请求体(body):

请求体被放置在`invitation`中，invitation是一个数组，但是数组中只有一个元素，元素的字段如下

|字段|说明|例子|
|-|-|-|
|token|从邮箱点击链接之后会带上一个token，在接受邀请的时候需要带上|xxxxxxxxxxxxxxxx|
|password|密码|123456|
|email|邮箱|guoshencheng1@gmail.com|
|name|用户名|guoshencheng|

### 查看邀请状态

请求路径: `/authentication/invitation`</br>
请求类型: get</br>

query:

|字段|说明|例子|
|-|-|-|
|email|邮箱|guoshencheng1@gmail.com|

### 查看是否激活了用户体系

请求路径: `/authentication/setup`</br>
请求类型: get</br>

返回例子：

```json
{
  "setup": [
    {
      "status": true
    }
  ]
}
```


### 查看设置

请求路径: `/authentication/configuration`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

返回例子：

```json
{
  "configuration": [{
    "key": "fileStorage",
    "value": true
  }, {
    "key": "publicAPI",
    "value": false
  }, {
    "key": "apps",
    "value": false
  }, {
    "key": "version",
    "value": "0.7.4"
  }, {
    "key": "environment",
    "value": "production"
  }, {
    "key": "database",
    "value": "mysql"
  }, {
    "key": "mail"
  }, {
    "key": "blogUrl",
    "value": ""
  }, {
    "key": "blogTitle",
    "value": ""
  }, {
    "key": "routeKeywords",
    "value": ""
  }, {
    "key": "storage",
    "value": ""
  }]
}
```

### 查看某项设置

请求路径: `/authentication/configuration/:key`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

参数

|字段|说明|例子|
|-|-|-|
|key|设置的字段名|`fileStorage`|

返回例子：

```json
{
  "configuration": [{
    "key": "fileStorage",
    "value": true
  }]
}
```

### 获取所有的博客

请求路径: `/posts`</br>
请求类型: get</br>

query:

|字段|说明|例子|
|-|-|-|
|page|页码，从1开始|1|
|limit|每一页的大小|20|
|status|文章的状态|`published`|
|tag|标签的id|1|


### 创建博客

请求路径: `/posts`</br>
请求类型: post</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

query:

|字段|说明|例子|
|-|-|-|
|include|额外的字段|`tag`|

请求体，放置在posts字段中，posts是一个数组，数据放置在第一个对象中：

|字段|说明|例子|
|-|-|-|
|title|标题|标题|
|slug|汉子转拼音|han-zi-zhuang-ping-yin|
|published_by|发布者id|1|
|author|作者id|1|
|featured|不明|true|
|image|首屏图片|xxx.xxx.xx/xx.png|
|language|语言|zh_CN|
|markdown|文章的内容|`## 文章的头部`|
|meta_description|页面meta描述|`文章的描述`|
|meta_title|页面meta标题|`文章的标题`|
|page|不明|false|
|status|博客的状态|"draft"|
|tags|博客的标签|[1]|

### 获取博客信息

请求路径: `/posts/:id`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

params:

|字段|说明|例子|
|-|-|-|
|id|博客的id|4|


### 更新博客

请求路径: `/posts`</br>
请求类型: put</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体：

和创建博客相同，多加上一个id字段


### 删除博客

请求路径: `/posts/:id`</br>
请求类型: delete</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 获取所有存储在数据库中的设置

请求路径: `/settings`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 获取存储在数据库中的某个设置

请求路径: `/settings/:key`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 更新存储在数据库中的某个设置

请求路径: `/settings`</br>
请求类型: put</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体：

|字段|说明|例子|
|-|-|-|
|settings|需要更新的设置的数组|`[{ id:xx, key: 'xxx' }]`|


### 获取所有用户

请求路径: `/users`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
query:

|字段|说明|例子|
|-|-|-|
|page|页码|1|
|limit|每一页的大小|20|
|status|状态|`active`, `warn-1`|


### 获取某个用户

请求路径: `/users/:id`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
params:

|字段|说明|例子|
|-|-|-|
|id|用户id|1|

### 通过邮箱获取某个用户

请求路径: `/users/email/:email`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
params:

|字段|说明|例子|
|-|-|-|
|email|用户邮箱|guoshencheng1@gmail.com|

### 修改密码

请求路径: `/users/password`</br>
请求类型: put</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体:

字段都放置在password中，放置其对应数组的第一个对象

|字段|说明|例子|
|-|-|-|
|user_id|用户id|1|
|oldPassword|原先的密码|1234578|
|newPassword|新的密码|1234578|
|ne2Password|确认的新的密码|1234578|

### 修改用户信息

请求路径: `/users/:id`</br>
请求类型: put</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体:

字段都放置在users中，放置其对应数组的第一个对象，对象为user的普遍对象，可查看查询user的返回

### 创建用户

请求路径: `/users`</br>
请求类型: post</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体: 待补充...

### 删除用户

请求路径: `/users/:id`</br>
请求类型: delete</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 修改博客所属

请求路径: `/users/owner`</br>
请求类型: put</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

请求体：

待补全...


### 获取所有的角色

请求路径: `/roles`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 获取登陆的client信息

> 用于登陆时使用，默认的slug有ghost-frontend，ghost-admin

请求路径: `/clients/slug/:slug`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>


### 获取主题

请求路径: `/themes`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 获取所有标签

请求路径: `/tags`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 通过id获取某个标签

请求路径: `/tags/:id`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 通过slug来获取某个标签

请求路径: `/tags/slug/:slug`</br>
请求类型: get</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 创建tag

请求路径: `/tags`</br>
请求类型: post</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体:

外层字段放置在tags中，数据放置在tags的第一个元素中

|字段|说明|例子|
|-|-|-|
|name|标签名|新增标签|
|slug|标签名的英文字符化|xin-zeng-biao-qian|
|description|描述|xxxxxxxxxxxxxx|
|meta_title|seo标题|xxxxxxxxxxxxxx|
|meta_description|seo描述|xxxxxxxxxxxxxx|
|image|封面图|xxx.xx.xx/xx.png|
|hidden|是否隐藏|false|

### 更新tag

请求路径: `/tags/:id`</br>
请求类型: put</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>
请求体:

外层字段放置在tags中，数据放置在tags的第一个元素中

|字段|说明|例子|
|-|-|-|
|id|标签id|1|
|name|标签名|新增标签|
|slug|标签名的英文字符化|xin-zeng-biao-qian|
|description|描述|xxxxxxxxxxxxxx|
|meta_title|seo标题|xxxxxxxxxxxxxx|
|meta_description|seo描述|xxxxxxxxxxxxxx|
|image|封面图|xxx.xx.xx/xx.png|
|hidden|是否隐藏|false|

### 删除标签

请求路径: `/tags/:id`</br>
请求类型: delete</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

### 上传图片
请求路径: `/uploads`</br>
请求类型: post</br>
请求头：[请查看私有权限api头部规范](#登陆头部校验)</br>

请求体（mutipart）:

图片的字段名 uploadimage


get `/slugs/:type/:name` public

get `/notifications` private

post `/notifications` private

delete `/notifications/:id` private

post `/mail` private
post `/mail/test` private

post `/authentication/setup` private
put `/authentication/setup` private
