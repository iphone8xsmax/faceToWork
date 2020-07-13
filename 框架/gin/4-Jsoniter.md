使用 jsoniter 编译

Gin 使用 ``encoding/json ``作为默认的 json 包，但是你可以在编译中使用标签将其修改为 jsoniter。
```
$ go build -tags=jsoniter .
```
