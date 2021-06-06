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

We can use the following code (save it in a file called swararoy-http-server.go

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
