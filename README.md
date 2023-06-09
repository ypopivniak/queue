# Redis queue library for Golang

go-redis-queue library defines basic `Queue` interface implements queues using:
- Redis [Lists](https://redis.io/docs/data-types/lists/) - `ListQueue`
- Redis [Streams](https://redis.io/docs/data-types/streams/) - `StreamQueue` (TBD)

It uses
[MessagePack](https://github.com/vmihailenco/msgpack) to marshal values.

## Installation

go-redis-queue supports 2 last Go versions and requires a Go version with
[modules](https://github.com/golang/go/wiki/Modules) support. So make sure to initialize a Go
module:

```shell
go mod init github.com/my/repo
```

And then install go-redis-queue:

```shell
go get github.com/ypopivniak/go-redis-queue
```

## Quickstart

```go
package main

import (
	"context"
	"fmt"
	"log"
	"time"

	"github.com/go-redis/redis/v8"
	"github.com/ypopivniak/go-redis-queue"
)

type Object struct {
	Str string
	Num int
}

func main() {
	ctx := context.TODO()
	
	// Construct ®Redis client
	client := redis.NewClient(&redis.Options{
		Addr: "localhost:6379",
	})
	
	// Construct ListQueue
	q := queue.NewListQueue(client, &queue.Options{})

	// Enqueue value
	input := Object{"foo", 69}
	err := q.Enqueue(ctx, "queue:name", &input)
	if err != nil {
		log.Fatal(err)
	}

	// Dequeue value
	var output Object
	err := queue.Dequeue(ctx, "queue:name", &output)
	if err != nil {
		log.Fatal(err)
	}
	log.Printf("%v", output)
}
```
