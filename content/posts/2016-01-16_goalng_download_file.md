---
title: "go下载"
categories: [ "代码人生" ]
tags: [ "golang" ]
draft: false
slug: "goalng_download_file"
date: "2016-01-16 14:40:00"
url: "goalng_download_file.html"
---

今天处理GO下载的时候遇到一个问题：如果调用readall去读取response，会造成很大的内存消耗，并且无法获取下载的进度。

直到搜到[ioutil.ReadAll(httpResponse.Body) memory consumption][1]，才解决了我的问题。


<!--more-->


```golang
package main

import (
	"fmt"
	"io"
	"net/http"
	"os"
)

func main() {
	url := "http://dl.360safe.com/setup.exe"

	resp, _ := http.Get(url)

	filename := "setup.exe"

	f, _ := os.OpenFile(filename, os.O_CREATE|os.O_RDWR|os.O_TRUNC, 0644)

	n, err := copyBuffer(f, resp.Body, nil)

	if err != nil {
		fmt.Println(err, n)
	}
}

func copyBuffer(dst io.Writer, src io.Reader, buf []byte) (written int64, err error) {
	// If the reader has a WriteTo method, use it to do the copy.
	// Avoids an allocation and a copy.
	if wt, ok := src.(io.WriterTo); ok {
		return wt.WriteTo(dst)
	}
	// Similarly, if the writer has a ReadFrom method, use it to do the copy.
	if rt, ok := dst.(io.ReaderFrom); ok {
		return rt.ReadFrom(src)
	}
	if buf == nil {
		buf = make([]byte, 1024*1024)
	}
	for {
		nr, er := src.Read(buf)
		if nr > 0 {
			nw, ew := dst.Write(buf[0:nr])
			if nw > 0 {
				written += int64(nw)
			}
			if ew != nil {
				err = ew
				break
			}
			if nr != nw {
				err = io.ErrShortWrite
				break
			}
		}
		if er == io.EOF {
			break
		}
		if er != nil {
			err = er
			break
		}
	}
	return written, err
}

```


其中copyBuffer的written就可以解决下载进度的问题，并且使用了io.copy打通了读和写的通道，很优雅的解决了问题。





  [1]: https://www.reddit.com/r/golang/comments/2bmnoq/ioutilreadallhttpresponsebody_memory_consumption/