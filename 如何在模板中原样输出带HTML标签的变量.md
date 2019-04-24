#### 数据库中有一些字段值是由富文本编辑器编写存储的，那么我们在取的时候想把标签页带上.
#### 方案1：其实我一开始是使用方案2的，因为对beego不是很熟，我从beego的源码包里面(github.com/astaxie/beego/template.go)找到了下面一段代码
```
func init() {
	beegoTplFuncMap["dateformat"] = DateFormat
	beegoTplFuncMap["date"] = Date
	beegoTplFuncMap["compare"] = Compare
	beegoTplFuncMap["compare_not"] = CompareNot
	beegoTplFuncMap["not_nil"] = NotNil
	beegoTplFuncMap["not_null"] = NotNil
	beegoTplFuncMap["substr"] = Substr
	beegoTplFuncMap["html2str"] = HTML2str
	beegoTplFuncMap["str2html"] = Str2html
	beegoTplFuncMap["htmlquote"] = Htmlquote
	beegoTplFuncMap["htmlunquote"] = Htmlunquote
	beegoTplFuncMap["renderform"] = RenderForm
	beegoTplFuncMap["assets_js"] = AssetsJs
	beegoTplFuncMap["assets_css"] = AssetsCSS
	beegoTplFuncMap["config"] = GetConfig
	beegoTplFuncMap["map_get"] = MapGet

	// Comparisons
	beegoTplFuncMap["eq"] = eq // ==
	beegoTplFuncMap["ge"] = ge // >=
	beegoTplFuncMap["gt"] = gt // >
	beegoTplFuncMap["le"] = le // <=
	beegoTplFuncMap["lt"] = lt // <
	beegoTplFuncMap["ne"] = ne // !=

	beegoTplFuncMap["urlfor"] = URLFor // build a URL to match a Controller and it's method
}
```
#### 我发现了str2html这个自定义函数，追了一下代码，然后使用了一下，结果显示是正常的
```
<p class="card-text">{{.Content | str2html}}</p>
```
#### 方案2：我在main.go中增加了一个自定义函数
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
