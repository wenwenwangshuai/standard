# 命名规范

### 命名方法

#### 1.驼峰命名法

大驼峰为首字母大写：EmployeeId 小驼峰为首字母小写：employeeId

```
// bad
EmployeeID
UserAPI

// good
EmployeeId
employeeId

UserApi
userApi
```

#### 2.串行命名法

单词之间通过连字符“-”连接

```
background-color
header-logo
```

#### 3.蛇形命名法

单词之间通过下划线“_”连接，比如“”

```
MAX_LIMIT
max_limit
```

### 约定规范

1. 类命名是名词，表示一个对象，采用大驼峰命名法：class Account

2. 方法命名是动词或动宾短语，表示一个动作，采用小驼峰命名法：function translateArticle()

3. 变量命名，采用小驼峰命名法：userName

4. 常量命名，采用大写蛇形命名法：MAX_NUM

5. 文件模块命名是名词，采用小驼峰命名法：exportExcel.js

6. 组件命名是名词，采用大驼峰命名法：UserHome.tsx（index.tsx除外）

7. css类命名采用串行命名法：.header-logo

8. 图片命名采用串行命名法：add-plus.png

9. 目录命名采用串行命名法：user-list

10. 路由路径命名小驼峰命名法：/pay/payInfo

11. 项目命名采用串行命名法：flow-portal