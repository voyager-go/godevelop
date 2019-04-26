#### 一些读者是有其他后端语言开发经验的，许多框架的orm在设置了unique唯一约束后，更新单条记录的某个字段值，判断是否唯一的时候都会把自己当前的值给忽略掉，不忽略自身的话怎么更新都是”值已经存在“。那么我们在beego的开发中该如何解决此问题呢？
#### 我翻了一下文档，发现文档中并没有提到这一点，当然也有可能是我看的不够详细吧，我就简单说一下我想到的解决思路吧。
#### 场景：username和phone被我设置了unique，现在我要更新它们并且判断新输入的值是否唯一，我可以先根据name与id去查，释义为：除了这条id之外是否还存在相同的name。
```
	o := orm.NewOrm()
	u := models.UserModel{}
	err := o.
		QueryTable("users").
		Exclude("id", this.User.Id).
		Filter("name", username).
		One(&u)
	if err == nil {}
```
