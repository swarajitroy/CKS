# Minimize Base Image Footprint

## Context
---

- The Go programming language is a natural fit for containers because it can compile down to a single statically-linked binary.  
And if you place that single executable on top of scratch, a distroless image, or a small image like alpine, your final image has a minimal footprint which is great for 
consumption and reuse.

- This needs that the host machine doing the compilation of the Go source code, and having all the Go build tools and external packages so that it can produce that final executable; which it then copies onto the image.

- This  is not a good fit for an automated CI/CD pipeline.  For an automated pipeline, it is better to have the source code copied into a base container that contains the exact set of build tools, packages, and dependencies that can then faithfully produce the final executable. The only problem now is that the image produced may have tens or hundreds of megabytes of build tools and cruft sitting inside, while all we need is the final executable.

- multi-stage builds are able to address this issue.  We can use a temporary intermediate image to do the build/compilation/linking, and then take the result and copy it into a clean final image that only contains the final executable.

## The application
---

We can use the following code (save it in a file called swararoy-http-server.go)

```

package main

import (
    "fmt"
    "net/http"
)

func hello(w http.ResponseWriter, req *http.Request) {

    fmt.Fprintf(w, "hello\n")
}

func headers(w http.ResponseWriter, req *http.Request) {

    for name, headers := range req.Header {
        for _, h := range headers {
            fmt.Fprintf(w, "%v: %v\n", name, h)
        }
    }
}

func main() {

    http.HandleFunc("/hello", hello)
    http.HandleFunc("/headers", headers)

    http.ListenAndServe(":8090", nil)
}


```

## The image without Multistage build 
---

Create a Dockerfile.

```

FROM golang:1.13-alpine3.11
RUN mkdir /build
ADD *.go /build/
WORKDIR /build
RUN CGO_ENABLED=0 GOOS=linux go build -a -o swararoy-http-server .

# executable
ENTRYPOINT [ "./swararoy-http-server" ]


```

Do a Docker built

```

ubuntu@ip-172-31-22-219:~/go_server$ ls
Dockerfile  swararoy-http-server.go
ubuntu@ip-172-31-22-219:~/go_server$ sudo docker build -t swararoy-http-server:v1 .
Sending build context to Docker daemon  3.072kB
Step 1/6 : FROM golang:1.13-alpine3.11
1.13-alpine3.11: Pulling from library/golang
cbdbe7a5bc2a: Pull complete
408f87550127: Pull complete
fe522b08c979: Pull complete
274b9d9af2b4: Pull complete
53955a7f64a2: Pull complete
Digest: sha256:ec6dcf15073c307fbcfc3149efe8835f3ec2bd0a0cb49aaaee4949cfc4c86b65
Status: Downloaded newer image for golang:1.13-alpine3.11
 ---> eff4fd40cebc
Step 2/6 : RUN mkdir /build
 ---> Running in 4e02f9677851
Removing intermediate container 4e02f9677851
 ---> eec6e86535aa
Step 3/6 : ADD *.go /build/
 ---> 631e857650b1
Step 4/6 : WORKDIR /build
 ---> Running in 1fb612e8ac7d
Removing intermediate container 1fb612e8ac7d
 ---> 9671900cb8f8
Step 5/6 : RUN CGO_ENABLED=0 GOOS=linux go build -a -o swararoy-http-server .
 ---> Running in 859c632d95e6
Removing intermediate container 859c632d95e6
 ---> 8a45250854ad
Step 6/6 : ENTRYPOINT [ "./swararoy-http-server" ]
 ---> Running in 96d8aeeaa538
Removing intermediate container 96d8aeeaa538
 ---> bd043497a603
Successfully built bd043497a603
Successfully tagged swararoy-http-server:v1

```
Run the image

```
ubuntu@ip-172-31-22-219:~/go_server$ sudo docker run -d -p 8090:8090 swararoy-http-server:v1
be84a490c1a90b348a36104145e87f4494d01fb5d523f9bd44446b0a20693056
ubuntu@ip-172-31-22-219:~/go_server$ curl localhost:8090/hello
hello


```
Inspect the size of the image - almost a 400 MB image

```
ubuntu@ip-172-31-22-219:~/go_server$ sudo docker image ls
REPOSITORY             TAG               IMAGE ID       CREATED          SIZE
swararoy-http-server   v1                bd043497a603   27 minutes ago   393MB

```
