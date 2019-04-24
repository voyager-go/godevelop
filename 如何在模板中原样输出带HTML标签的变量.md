#### 数据库中有一些字段值是由富文本编辑器编写存储的，那么我们在取的时候想把标签页带上.
#### 我在main.go中增加了一个自定义函数
```
func unescaped (htmlContent string) interface{} {
	return template.HTML(htmlContent)
}
```
#### 然后在main函数中注册
```
func main(){
	beego.AddFuncMap("unescaped", unescaped)
	beego.Run()
}
```
#### 最后就可以在模板中使用了
```
<p class="card-text">{{.Content | unescaped}}</p>
```
