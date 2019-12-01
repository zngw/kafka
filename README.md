# kafka
## 生产者测试producer.go
```go
package main

import (
	"github.com/zngw/kafka"
	"github.com/zngw/log"
	"os/signal"
	"runtime"
	"syscall"
)

func main() {
	// 初始化日志
	err := log.Init(nil)
	if err != nil {
		panic(err)
	}

	// 初始化生产生
	err = kafka.InitProducer("192.168.1.29:9092")
	if err != nil {
		panic(err)
	}

	// 关闭
	defer kafka.Close()

	// 发送测试消息
	kafka.Send("Test","This is Test Msg")
	kafka.Send("Test","Hello Guoke")

	signal.Ignore(syscall.SIGHUP)
	runtime.Goexit()
}

```
## 消费者测试consumer.go
```go
package main

import (
	"github.com/zngw/kafka"
	"github.com/zngw/log"
	"os/signal"
	"runtime"
	"syscall"
)

func main() {
	// 初始化日志
	err := log.Init(nil)
	if err != nil {
		panic(err)
	}

	// 初始化消费者
	err = kafka.InitConsumer("192.168.1.29:9092")
	if err != nil {
		panic(err)
	}

	// 监听
	go func() {
		err = kafka.LoopConsumer("Test", TopicCallBack)
		if err != nil {
			panic(err)
		}
	}()

	signal.Ignore(syscall.SIGHUP)
	runtime.Goexit()
}

func TopicCallBack(data []byte) {
	log.Trace("kafka", "Test:"+string(data))
}
```
## 执行结果
![](result.png)