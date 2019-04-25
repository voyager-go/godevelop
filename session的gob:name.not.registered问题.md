##### 在使用beego的时候，用到了session，并采用文件存储
##### 在开发的过程中，每隔一段时间控制台就会报出 gob: name not registered for interface: 的错误，并且页面报错。
##### 搜索后发现这是一个大家都经常遇到的bug，并且没有比较好的解决方案。
##### 这里找到了两种解决办法：
##### 1. 使用golang自带的gob包对结构体进行注册如gob.Register(models.UserModel{})
```
// 示例代码
func init() {
	gob.Register(models.UserModel{})
}
```
##### 2. 使用内存存储session
