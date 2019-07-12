##### 作者在使用gorm的时候，在一个包里定义了全局变量，可是在方法中却改变了变量的地址，于是报错如下：
```
runtime error: invalid memory address or nil pointer dereference
```
##### 代码大致如下
```
package models

import (
	"github.com/jinzhu/gorm"
	_ "github.com/jinzhu/gorm/dialects/mysql"
	"log"
)
var DB *gorm.DB
func init()  {
	var (
		err error
		dbType, dbName, user, password, host, tablePrefix string
	)

DB, err := gorm.Open(dbType, fmt.Sprintf("%s:%s@tcp(%s)/%s?charset=utf8&parseTime=True&loc=Local"
......
}
```
##### 注意上述代码的 DB, err :=
##### 正是这个冒号导致的报错
