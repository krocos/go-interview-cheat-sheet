Проверка типа переменной, а не её значения.

Может быть в виде `switch`
```go
func checkType(i interface{}) {
  switch i.(type) {
  case int:
    println("is integer")

  case string:
    println("is string")

  default:
    println("has unknown type")
  }
}
```

Можно проверить тип через `if`
```go
var any interface{}

any = "foobar"

if s, ok := any.(string); ok {
	println("this is a string:", s)
}
```
