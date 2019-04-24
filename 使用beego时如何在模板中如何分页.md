#### 今天做列表的时候，需要给数据进行分页，于是我参考了一下文档，发现文档对分页的介绍非常简陋，按照文档上的做法写了一会儿，很遗憾没有成功...当然也和我不想按照文档上的方法去实现有关，懒得去研究了，最后我还是按照常规的方式进行分页，接下来我罗列一下分页相关的代码。
#### 控制器中的方法有两个，一个是渲染页面，展示基本数据，另一个方法是异步加载列表数据
```
var PageCount = 1
// @router / [get]
func (this *IndexController) Index(){
	count, perr := orm.NewOrm().QueryTable("poetry").Count()
	if perr == nil {
		this.Data["Count"]     = count
		this.Data["PageCount"] = PageCount
	}
	this.TplName = "index.html"
}
// @router /asynloadpoetry [post]
func (this *IndexController) AsynLoadPoetry() {
	currentPage,_ := this.GetInt("currentPage")
	if currentPage < 1 {
		currentPage = 1
	}
	offset := (currentPage - 1) * PageCount	// 偏移量
	var poetry []models.PoetryModel
	_, perr := orm.NewOrm().QueryTable("poetry").Offset(offset).Limit(PageCount).All(&poetry)
	if perr == nil {
		this.Data["json"] = map[string]interface{}{
			"code": 200,
			"msg" : "",
			"data": poetry,
		}
		this.ServeJSON()
	}
}
```
#### 代码还是比较简单的，主要还是利用了偏移量进行常规的分页，接下来就是前端的代码，这里需要用到一个jquery的分页插件，叫jquery.pagination.js，这里需要提醒的是要记得依赖jquery(高一点的版本)
```
// html 部分
<div class="col-10">
    <div class="y-poetries">
    </div>
   <div class="pagination"></div>
 </div>
```
#### js部分的代码主要是对我自己的dom结构进行封装的函数，读者可按照自己的需求来定制代码
```
// js部分
<script src="/static/js/jquery.min.js"></script>
<script src="/static/js/jquery.pagination.js"></script>
<script>
    $(function () {
        appendPoetryContainer(1)
    })
    $('.pagination').pagination({
        showData:{{.PageCount}},
        coping: true,
        keepShowPN: true,
        totalData: {{.Count}},
        homePage: '首页',
        endPage: '末页',
        prevContent: '上一页',
        nextContent: '下一页',
        callback: function (api) {
            appendPoetryContainer(api.getCurrent())
        }
    });
    /**
     *向诗词的承载容器中追加HTML
     */
    function appendPoetryContainer(current_page) {
        $.post('/asynloadpoetry', {currentPage:current_page}, function (response) {
            let poetryContainer = $('.y-poetries');
            poetryContainer.empty();
            let poetryHtml = '';
            $.each(response.data, function (index, value) {
                poetryHtml += generatePoetryHtml(value)
            });
            poetryContainer.append(poetryHtml)
        } , 'json')
    }
    /**
     * 生成诗词列表中单个列表的HTML
     * @param data
     * @returns {string|*}
     */
    function generatePoetryHtml(data) {
        poetryHtmlTmp =
            '<div class="card">\n' +
            ' <div class="card-body y-card-body">\n' +
            '    <h6 class="card-title"><a href="/post/'+data.Id+'">'+data.Title+'</a></h6>\n' +
            '    <div class="card-text">'+data.Content+'</div>\n' +
            '    <p></p>\n' +
            '    <p class="y-card-text">百科：<a href="'+data.BaikeLink+'" target="_blank">'+data.Title+'百科</a> </p>\n' +
            '    <p class="y-card-text">故事：<a href="'+data.StoryLink+'" target="_blank">'+data.Title+'扩展</a> </p>\n' +
            '    <p class="y-card-text">视频：<a href="'+data.VideoLink+'" target="_blank">'+data.Title+'视频</a> </p>\n' +
            '    <p class="y-card-text"><a href="">youeryuan</a>发布于：'+Format(data.Created,"yyyy-MM-dd HH:mm:ss")+'</p>\n' +
            ' </div>\n' +
            '</div>';
        return poetryHtmlTmp
    }
</script>
```