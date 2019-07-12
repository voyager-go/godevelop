##### 什么是jwt-go ?
##### 可以理解为json web tokens的golang实现
##### https://github.com/dgrijalva/jwt-go
##### 作者在使用jwt-go的时候遇到了一个报错
```
key is of invalid type
```
##### 涉及的部分代码为
```
var jwtSecret = []byte("string-xxxx-string")
//tokenClaims := jwt.NewWithClaims(jwt.SigningMethodES256, claims)
tokenClaims := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
fmt.Println(string(jwtSecret))
token, err := tokenClaims.SignedString(jwtSecret)
fmt.Printf("%v %v\n", token, err) // key is of invalid type
```
##### 根据上面代码，作者发现两个地方的错误会导致报错
##### 1. 注意tokenClaims.SignedString(jwtSecret)的参数jwtSecret为[]byte()而不是string
##### 2. 注意jwt.NewWithClaims的加密方法用的是jwt.SigningMethodHS256而不是jwt.SigningMethodES256
