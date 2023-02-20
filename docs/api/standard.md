# 接口规范v1.0.0

### 1. 规范原则

* 接口返回数据即显示：前端仅做渲染逻辑处理；
* 渲染逻辑禁止跨多个接口调用；
* 前端关注交互、渲染逻辑，尽量避免业务逻辑处理的出现；
* 请求响应传输数据格式：JSON，JSON数据尽量简单轻量，避免多级JSON的出现；

### 2. 响应基本格式

```json
{
    errcode: 0,
    errmsg: '成功'
    data: {}
}
```

`errcode`: 请求处理状态

* 0: 请求处理成功
* 500: 请求处理失败
* 401: 请求未认证，跳转登录页

`errmsg`: 请求处理信息，前端根据errcode非0时，提示此errmsg文案

### 3. 列表分页格式

**请求**

```json
{
  pageNo: 1,
  pageSize: 10
}
```

**响应**

```json
{
  errcode: 0,
  errmsg: '成功'
  data: {
    total: 2,
    pageNo: 1,
    pageSize: 10,
    list: []
  }
}
```

* data.total    总记录数
* data.pageNo   当前页码
* data.pageSize 每页大小
* data.list     列表数据 
