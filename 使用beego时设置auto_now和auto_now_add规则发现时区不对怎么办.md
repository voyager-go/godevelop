#### 在beego的orm中是可以添加规则自动去维护创建日期和更新日期的，只需要按照下面代码来写
```
Created time.Time `orm:"auto_now_add;type(datetime)"`
Updated time.Time `orm:"auto_now;type(datetime)"`
```
#### 可是我们这么写以后，新增和更新时发现自动填充的时间日期并不正确，那是因为在模型中初始化数据库的时候，没有设置时区
```
// 注意&loc=Asia%2FShanghai
orm.RegisterDataBase("default", "mysql", "root:123456@tcp(127.0.0.1:3306)/poetry?charset=utf8&loc=Asia%2FShanghai", 30)
orm.DefaultTimeLoc = time.Local
```
#### 修改代码后，发现时间日期正常
