---
layout: post
title:  "文件系统写入效率的测试"
date:   2022-08-10 14:30:05
categories: 
tags:  golang
excerpt: 用golang进行文件系统写入效率的测试
---

在文件系统的指定位置创建一个文件名随机的文件，并写入500kb内容随机的数据，将写入效率记录在日志中，以便于后期复盘分析。

主要用于主机对NAS写入效率的监控和探测。该小程序只会测试一次，需要搭配shell脚本或定时任务进行实际部署。

```go
// 文件写入效率测试
// 必须有一个参数，为一个文件系统上已存在的路径，可以是相对路径也可以是绝对路径
// 会向参数指定的路径中写入一个 500kb 随机内容的文件
// 随机文件的文件名为 testdata.日期时间

package main

import (
	"crypto/rand"
	"fmt"
	"log"
	"os"
	"path"
	"time"
)

// 读取输入参数并进行检查，如果参数为一个存在的目录，则在后面加上要生成的文件名，返回待路径的文件名。
func getDesFileName() string {
	var name string = ""
	if len(os.Args) != 2 {
		log.Fatalf("Useage: \n %s {des path}\n", os.Args[0])
	}
	var desDir = os.Args[1]
	dirStat, err := os.Stat(desDir)
	if err != nil {
		log.Fatalf("Useage: \n %s {des path}\n %v\n", os.Args[0], err)
	}
	if !dirStat.IsDir() {
		log.Fatalf("%s is NOT a directory.\n %v\n", os.Args[1], err)
	}
	timeTail := time.Now().Format("20060102150405")
	name = path.Join(desDir, "testdata."+timeTail)
	return name
}

// 创建由name指定的文件，并将d的字节数组写入文件内
func WriteTest(name string, d []byte) {
	fp, err := os.Create(name)
	if err != nil {
		log.Fatalf("file write fail. \n %v ", err)
	}
	fp.Write(d)
	fp.Close()
}

// 生成长度为n的内容随机的字节数组
func makeData(n int) []byte {
	if n <= 0 {
		return []byte{}
	}
	b := make([]byte, n)
	_, err := rand.Read(b[:])
	if err != nil {
		return []byte{}
	}
	return b
}

const (
	fileSize int = 512000 // 指定生成的文件字节长度
)

// 初始化指定log日志模式和位置
func init() {
	logFileName := "./log/testWrite-" + time.Now().Format("20060102") + ".log"
	logFile, err := os.OpenFile(logFileName, os.O_CREATE|os.O_WRONLY|os.O_APPEND, 0644)
	if err != nil {
		fmt.Println("Open log File err: ", err)
		os.Exit(2)
	}
	log.SetOutput(logFile)
}

func main() {
	desFileName := getDesFileName()                       // 拿到目标文件名，带路径
	data := makeData(fileSize)                            // 生成fileSize长度的字节数组
	timeStart := time.Now()                               // 开始计时
	WriteTest(desFileName, data)                          // 创建文件，将生成的字节，写入文件中
	time_interval := time.Since(timeStart).Microseconds() // 获得与timeStart的时间间隔，单位为微秒
	log.Printf("Test: Create file[%s] and Write %d Bytes data finished in %d Microseconds.",
		desFileName, fileSize, time_interval) // 写入日志，记录情况
	os.Remove(desFileName) // 删除测试创建的文件
}
```


执行效果，在当前目录写入文件：
```bash
go run . ./
```

日志输出：
```log
2022/08/10 14:38:29 Test: Create file[testdata.20220810143829] and Write 512000 Bytes data finished in 714 Microseconds.
```