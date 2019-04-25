### 背景
#### 今天在写用户评论模块的时候，遇到了取用户评论数据的问题，根据文档上的查询操作，取出了用户的评论内容，可是我该如何根据表中的user_id来取用户的头像、姓名等属性呢？
### 解决思路
#### beego的orm很好的实现了关联查询，但是很多没有认真读文档的朋友或许还是比较迷惑，这里我来简单实现一下。
### 表关系 评论表 用户表
```
// 用户评论
type CommentModel struct {
	Id int   `orm:"pk;auto"`
	Pid      int
	PoetryId int	  // 注：诗词的ID
	Content  string
	Status   int8	  `orm:"default(1)" description:(这是状态字段1表示正常0表示禁用)`
	Created  time.Time `orm:"auto_now_add;type(datetime)"`
	// 下面这行代码是重点
	User  *UserModel  `orm:"rel(fk)"`
}

// 普通用户
type UserModel struct {
	Id 		  int `orm:"pk;auto"`
	Avatar    string
	Name 	  string `orm:"unique;size(60)"`
	Phone 	  string `orm:"size(11);unique"`
	Password  string `orm:"size(32)"`
	LoginIp   string `orm:"size(15)"`
	Status    int8	  `orm:"default(1)" description:(这是状态字段1表示正常0表示禁用)`
	Created time.Time `orm:"auto_now_add;type(datetime)"`
	Updated time.Time `orm:"auto_now;type(datetime)"`
}
```
#### 评论表中(结构体)虽然没有明确的写出user_id，但是orm在执行表的生成操作的时候，会自动生成user_id字段，当我们在查询所有相关的评论时，可以直接取到用户的信息
```
	o := orm.NewOrm()
	var comments []models.CommentModel
	// 查询评论
	_, err = o.QueryTable("comments").
		Filter("poetry_id", id).
		Filter("status", models.STATUS_TRUE).
		RelatedSel().
		All(&comments)
		// INNER JOIN user ...
		// 设置 expr 只对设置的字段进行关系查询
		// fmt.Println(comments)
	// 模板赋值
	if  err == nil {
		this.Data["Poetry"]  = poetry
		this.Data["Author"]  = author
		this.Data["Comment"] = comments
	}
```
#### 这段代码需要注意的是RelatedSel()，这里面也可以写上RelatedSel("user")
#### 数据取到了，那么我们如何在模板中使用呢？请看下面一段代码。
```
{{range $index, $elem := .Comment}}
	<div class="y-single-comment">
		<div class="y-comment-author-avatar">
			<img src="/static{{$elem.User.Avatar}}" alt="">
		</div>
		<p class="card-text y-comment-content">
		<span class="y-comment-author-name">{{$elem.User.Name}}：</span>
			{{.Content}}
		<span class="y-comment-date">发布于：{{date .Created "Y-m-d H:i:s"}}</span>
		</p>
	</div>
{{end}}
```
#### 相关内容可查看beego的官方文档
#### [beego模型定义](https://beego.me/docs/mvc/model/models.md)
#### [beego高级查询](https://beego.me/docs/mvc/model/query.md#relatedsel)