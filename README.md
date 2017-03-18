# [Guetzli](https://github.com/google/guetzli) perceptual JPEG encoder for Go

[![GoDoc](https://godoc.org/github.com/chai2010/guetzli-go?status.svg)](https://godoc.org/github.com/chai2010/guetzli-go)


Install
=======

Install `GCC` or `MinGW` ([download here](http://tdm-gcc.tdragon.net/download)) at first,
and then run these commands:

1. `go get github.com/chai2010/guetzli-go`
2. `go run hello.go`


Benchmark
=========

```
go test -test.bench=.*
BenchmarkEncode_guetzli_quality_95-4   	       1	1043846782 ns/op
BenchmarkEncode_jpeg_quality_95-4      	    2000	   1130081 ns/op
BenchmarkEncode_png-4                  	     200	   6423543 ns/op
PASS
ok  	github.com/chai2010/guetzli-go	5.817s
```

Example
=======

This is a simple example:

```Go
package main

import (
	"bytes"
	"fmt"
	"image"
	"image/jpeg"
	_ "image/png"
	"io/ioutil"
	"log"
	"os"

	"github.com/chai2010/guetzli-go"
)

func main() {
	m0 := loadImage("./testdata/video-001.png")

	data1 := jpegEncode(m0, 95)
	data2 := guetzliEncode(m0, 95)

	fmt.Println("jpeg encoded size:", len(data1))
	fmt.Println("guetzli encoded size:", len(data2))

	if err := ioutil.WriteFile("a.out.jpeg", data1, 0666); err != nil {
		log.Println(err)
	}
	if err := ioutil.WriteFile("a.out.guetzli.jpeg", data2, 0666); err != nil {
		log.Println(err)
	}

	fmt.Println("Done")
}

func loadImage(name string) image.Image {
	f, err := os.Open(name)
	if err != nil {
		log.Fatal(err)
	}
	defer f.Close()
	m, _, err := image.Decode(f)
	if err != nil {
		log.Fatal(err)
	}
	return m
}

func jpegEncode(m image.Image, quality int) []byte {
	var buf bytes.Buffer
	err := jpeg.Encode(&buf, m, &jpeg.Options{Quality: quality})
	if err != nil {
		log.Fatal(err)
	}
	return buf.Bytes()
}

func guetzliEncode(m image.Image, quality float32) []byte {
	var buf bytes.Buffer
	err := guetzli.Encode(&buf, m, &guetzli.Options{Quality: quality})
	if err != nil {
		log.Fatal(err)
	}
	return buf.Bytes()
}
```

BUGS
====

Report bugs to <chaishushan@gmail.com>.

Thanks!
