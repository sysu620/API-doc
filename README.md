# API
挣闲钱API文档

# 前端请求
## 前端请求参数的数据类型

前端的请求参数为`json`类型，在请求的header中设置为`"Content-Type": "application/json"`

## 前端权限认证

用户登录后会获得一个token(令牌)，在前端发送除GET之外的请求时，都需将token赋给Headers的Authorization字段，才有权限对服务端数据进行修改



# 用户

## 用户注册
> `POST /user/signup`

**BODY**

```json
{
	"userId":     integer //用户id，注册时不需要赋值，由服务生成，置0即可
	"username":   string  //用户名，必填
	"password":	  string  //用户密码，必填
	"university": string  //用户所在大学，必填
	"grade":	  string  //用户所在年级，必填
	"phone":	  string  //用户手机号，必填	
	"major":	  string  //用户所在专业，必填
	"email":	  string  //用户邮箱，必填
	"balance":	  integer //用户余额，注册时置0即可
}
```

**参数示例**
```json
{
	"userId":     0
	"username":   "张三"
	"password":	  "123"
	"university": "中山大学"
	"grade":	  "大三"
	"phone":	  "13232323233"
	"major":	  "软件工程"
	"email":	  "123456789@qq.com"
	"balance":	  0
}
```

**返回示例**
- 201
```json
{
	"userId":     10001
	"username":   "张三"
	"password":	  ""
	"university": "中山大学"
	"grade":	  "大三"
	"phone":	  "13232323233"
	"major":	  "软件工程"
	"email":	  "123456789@qq.com"
	"balance":	  0
}
```
- 400
```json
{
	"error": "用户名未填写"
}
```


## 用户登录
> `POST /user/signin`

**BODY**
```json
{
    "userId":   integer  //用户id，用户注册成功后会返回服务端生成的用户id
    "password": string   //用户密码
}
```

**参数示例**
```json
{
    "userId":   10001
    "password": "123"
}
```


**返回示例**
- 200
```json
{
	"token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJleHAiOjE1NjA5NDU4MDksImlhdCI6MTU2MDk0MjIwOX0.M6pDCT4IA0JWhtOLlv6BnejgqwGhxy8AIhLLiM6IxAM"
	//由服务端实时生成的token，记录了当前用户的登录状态
	//当客户端发送除GET外的请求时，需要将该token赋给Headers的Authorization字段
	//以表示有权限对服务端数据进行修改
}
```
- 400
```json
{
	"error": "用户名或密码错误"
}
```




## 获取用户的信息

> `GET /user/{userId}`

**Path Parameters**
```json
{
    "userId": integer //用户id
}
```


**返回示例**
- 200
```json
{
	"userId":     10001
	"username":   "张三"
	"password":	  ""
	"university": "中山大学"
	"grade":	  "大三"
	"phone":	  "13232323233"
	"major":	  "软件工程"
	"email":	  "123456789@qq.com"
	"balance":	  0
}
```
- 404

```json
{
	"error": "用户不存在"
}
```

## 用户登出

> `DELETE /user/signout`

**返回示例**
- 200

- 400


## 发布问卷任务

> `POST /user/publishQuery`

**BODY**
```json
{
	"task":{
	    "taskId":       integer //任务id，由服务端生成，置0即可
	    "taskType":     string  //任务类型，必填，分为"questionare"和"delivery"
	    "taskTitle":    string  //任务标题，必填  
	    "endTime":		string  //任务截止日期，必填
	    "userId":		integer //任务发起人id，为当前登录用户
	    "state":		string  //任务状态，分为"进行中"和"已完成"，由服务端更新
	},
	questionares:[{
		"questionareId": integer //问卷id，同任务id，由服务端生成，置0即可
		"num": 			 integer //题目序号，由服务端生成，客户端将问题按顺序存放即可
		"title":		 string  //题目内容，必填
		"type":		     string  //题目类型，必填，分为"radio"和"checkbox"
		"isNeed":        string  //该题是否必须填写，必填，分为"true"和"false"
		"options":       string  //将题目选项按顺序放置，必填，中间用,隔开
	},
	...
	]
}
```

**参数示例**
```json
{
	"task":{
	    "taskId":       0
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		0
	    "state":		""
	},
	questionares:[{
		"questionareId": 0
		"num": 			 0
		"title":		 "题目1"
		"type":		     "radio"
		"isNeed":        "true"
		"options":       "选项1,选项2,选项3"
	},
	...
	]
}
```


**返回示例**
- 200
```json
{
	"task":{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	questionares:[{
		"questionareId": 10000
		"num": 			 1
		"title":		 "题目1"
		"type":		     "radio"
		"isNeed":        "true"
		"options":       "选项1,选项2,选项3"
	},
	{
		"questionareId": 10000
		"num":			 2
		...
	},
	...
	]
}
```
- 400
```
{
	"error": "发布不成功"
}
```


## 完成/取消一个任务

> `PUT /user/finishPublish`

**BODY**
```json
{
    "taskId":  integer //任务Id
}
```

**参数示例**
```json
{
	"taskId":  10001
}
```


**返回示例**
- 200
- 404
```json
{
	"error": "任务不存在"
}
```


## 用户接取任务
> `POST /user/accept`

**BODY**
```json
{
	"taskId": integer
}
```

**返回示例**
- 201
```json
{
	"taskId": 10004,
	"userId": 10005,
	"state":  "已接取"
}
```
- 400
```json
{
	"error": "任务不存在"
}
```

## 用户完成任务
> `PUT /user/finishAccept`

**BODY**
```json
{
	"taskId": integer
}
```

**返回示例**
- 200
- 400
```json
{
	"error": "任务不存在"
}
```

# 任务
## 获取一个问卷任务
> `GET /task/questionare/{taskId}`

**Path Parameters**
```json
{
	"taskId": integer
}
```

**返回示例**
- 200
```json
{
	"task":{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	questionares:[{
		"questionareId": 10000
		"num": 			 1
		"title":		 "题目1"
		"type":		     "radio"
		"isNeed":        "true"
		"options":       "选项1,选项2,选项3"
	},
	{
		"questionareId": 10000
		"num":			 2
		...
	},
	...
	]
}
```
- 400
```json
{
	"error": "任务不存在"
}
```

## 获取一页用户接受的任务
> `GET /task/qAcceptPage?page=&userId=`

**Query Parameters**
```json
{
	"page":   integer //必填，如果page参数为0，则返回3个任务，供导航页使用；
					  //如果page大于0，则按页数返回10个任务
	"userId": integer //用户id
}
```

**返回示例**
- 200
```json
{
	"contents":[{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	...
	]
}
```
- 400
```json
{
	"error": "页数超出索引"
}
```

## 获取一页用户发布的任务
> `GET /task/qPublishPage?page=&userId=`

**Query Parameters**
```json
{
	"page":   integer //必填，如果page参数为0，则返回3个任务，供导航页使用；
					  //如果page大于0，则按页数返回10个任务
	"userId": integer //用户id
}
```

**返回示例**
- 200
```json
{
	"contents":[{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	...
	]
}
```
- 400
```json
{
	"error": "页数超出索引"
}
```

## 获取一页用户没接受过，也不是由用户发布的快递任务
> `GET /task/queryPageD?page=&userId=`

**Query Parameters**
```json
{
	"page":   integer //必填，如果page参数为0，则返回3个任务，供导航页使用；
					  //如果page大于0，则按页数返回10个任务
	"userId": integer //用户id
}
```

**返回示例**
- 200
```json
{
	"contents":[{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	...
	]
}
```
- 400
```json
{
	"error": "页数超出索引"
}
```

## 获取一页用户没接受过，也不是由用户发布的问卷任务
> `GET /task/queryPageQ?page=&userId=`

**Query Parameters**
```json
{
	"page":   integer //必填，如果page参数为0，则返回3个任务，供导航页使用；
					  //如果page大于0，则按页数返回10个任务
	"userId": integer //用户id
}
```

**返回示例**
- 200
```json
{
	"contents":[{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	...
	]
}
```
- 400
```json
{
	"error": "页数超出索引"
}
```

## 通过标题查询获取用户没接受过，也不是由用户发布的任务
> `GET /task/queryTitle?title=&userId=`

**Query Parameters**
```json
{
	"title":  string  //任务标题
	"userId": integer //用户id
}
```

**返回示例**
- 200
```json
{
	"contents":[{
	    "taskId":       10000
	    "taskType":     "questionare"
	    "taskTitle":    "问卷1"
	    "endTime":		"2019-06-21"
	    "userId":		10001
	    "state":		"进行中"
	},
	...
	]
}
```
- 400
```json
{
	"error": "页数超出索引"
}
```
