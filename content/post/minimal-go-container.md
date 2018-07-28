+++
categories = []
date = "2018-06-17T02:54:56-04:00"
image = "https://images.unsplash.com/photo-1493946740644-2d8a1f1a6aff?ixlib=rb-0.3.5&q=80&fm=jpg&crop=entropy&cs=tinysrgb&w=1080&fit=max&ixid=eyJhcHBfaWQiOjExNzczfQ&s=2a42149bdadb9808b62d87f71f08dab1"
tags = []
title = "Minimal Go Docker Container (122x smaller)"
weight = 5
writer = "Lukas Herman"

+++
Before we talk about how to make a small Go container, I want to talk a little bit about why we need it in the first place. According to Abby, an AWS evangelist, "smaller images mean faster build, and faster deploys. This also means a smaller attack surface" (check her video out [here](https://www.youtube.com/watch?v=pPsREQbf3PA&t=8s)). This brings us to these 3 important points:
* Speed
* Security
* Stability (This is an implicit benefit from the previous 2 points)


This is great, right? So, let's get started!

## Original Go Code
Following is a simple http server that will basically print "Hello world" on your browser.

*github.com/lherman-cs/scratch/app.go*
```go
package main

import (
	"log"
	"net/http"
)

func handler(w http.ResponseWriter, r *http.Request) {
	w.Write([]byte("Hello world"))
}

func main() {
	http.HandleFunc("/", handler)
	log.Fatal(http.ListenAndServe(":8080", nil))
}
```

If we run the code:
```sh
go run app.go
```
We would see the output as follow:
![Screenshot-from-2018-06-16-21-38-11](/content/images/2018/06/Screenshot-from-2018-06-16-21-38-11.png)

## Naïve Way to Dockerize Our Simple Web Server
*github.com/lherman-cs/scratch/Dockerfile.naive*
```docker
FROM golang:1.10.3

WORKDIR /go/src/app
COPY . .
RUN go build

EXPOSE 8080
ENTRYPOINT [ "./app" ]
```
This Dockerfile is fine. It will run as expected. But, if you check the size of this image, you will see that this simple image takes **a lot of storage** unnecessarily.

```sh
docker images
```
![Screenshot-from-2018-06-16-21-57-46](/content/images/2018/06/Screenshot-from-2018-06-16-21-57-46.png)

Yup! You see that right. It's 801MB. Can we improve that? Absolutely!

## Optimization #1: Use a Smaller Base Image
*github.com/lherman-cs/scratch/Dockerfile.alpine*
```docker
FROM golang:1.10.3-alpine

WORKDIR /go/src/app
COPY . .
RUN go build

EXPOSE 8080
ENTRYPOINT [ "./app" ]
```
*Note: Notice that the base image changed to* **golang:1.10.3-alpine**
This time, we simply changed the base image with a smaller image. The result is quite impressive!

```sh
docker images
```
![Screenshot-from-2018-06-16-21-58-32](/content/images/2018/06/Screenshot-from-2018-06-16-21-58-32.png)

From 801MB to 382MB, we have reduced the size by **2.097x**

## Optimization #2: Use Docker Multi-stage Build
*github.com/lherman-cs/scratch/Dockerfile.multi*
```docker
FROM golang:1.10.3-alpine AS builder

WORKDIR /go/src/app
COPY . .
RUN go build

FROM alpine
EXPOSE 8080

COPY --from=builder /go/src/app/app .
ENTRYPOINT [ "./app" ]
```
Wait... Why are there 2 base images? This is multi-stage build. Multi-stage build was introduced in **Docker 17.05**. This feature helps us to yet reduce our image size. [(read more about multi-stage build)](https://docs.docker.com/develop/develop-images/multistage-build/). The idea is to strip off all the dependencies that are needed **only for building** the program.

```sh
docker images
```
![Screenshot-from-2018-06-16-22-09-27](/content/images/2018/06/Screenshot-from-2018-06-16-22-09-27.png)

This way, we have reduced the size by **74.17x**

## Optimization #3: Use a Blank Image
*github.com/lherman-cs/scratch/Dockerfile.blank*
```docker
FROM golang:1.10.3-alpine AS builder

WORKDIR /go/src/app
COPY . .
RUN CGO_ENABLED=0 go build

FROM scratch
EXPOSE 8080

COPY --from=builder /go/src/app/app .
ENTRYPOINT [ "./app" ]
```
*Note: CGO_ENABLED=0 is to remove C dependencies*

Since Go is statically compiled, we know that Go programs have zero dependencies. Therefore, we can yet strip off all OS dependecies. Here, we use "scratch" as the base image. **Scratch** is the blank image, from which all other images built upon. By loading the go binary into this scratch image, we'll have a fully functional docker container with a very minimal size.

```sh
docker images
```
![Screenshot-from-2018-06-16-22-27-36](/content/images/2018/06/Screenshot-from-2018-06-16-22-27-36.png)

Finally, using this approach, we reduced the image size by **122.01x**

# Summary
![Screenshot-from-2018-06-16-22-32-34](/content/images/2018/06/Screenshot-from-2018-06-16-22-32-34.png)

In this article, we've successfully reduced our simple go web server image from **801MB** to just **6.56MB**. This will not only save your storage. This also means that you can download and deploy this container approximately **122.01x faster** (ignoring the download and deployment overhead). Again, smaller images **are faster, more secure, and more stable.**

*Check out the source code* [here](https://github.com/lherman-cs/scratch)