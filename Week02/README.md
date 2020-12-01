学习笔记

作业

1. 我们在数据库操作的时候，比如 dao 层中当遇到一个 sql.ErrNoRows 的时候，是否应该 Wrap 这个 error，抛给上层。为什么，应该怎么做请写出代码？

   答：应该用errors.Wrap去包住堆栈信息并抛给上层。主要是保住堆栈信息，方便地位问题。关键点是底层wrap错误，中间层做穿透，应用层做处理。

```go
package main
import(
    "database/sql"
    "github.com/pkg/errors"

    log "github.com/Sirupsen/logrus"
)

func main() {
    var id = "some_id"
  	biz(id)
}

func service(id string) error {
    return dao(id)
}

func dao(id string) error {
    var err = sql.ErrNoRows
    if err == sql.ErrNoRows {
        return errors.Wrap(err, "dao failed, id is "+ id)
    }
    return nil
}

func biz(id string)  {
    err :=  service(id)
  	if err != nil {
        log.Errorf("service failed, err is %+v", err)
      	return
    }
  	//do biz
}
```

