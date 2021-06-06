# Minimize Base Image Footprint


The Go programming language is a natural fit for containers because it can compile down to a single statically-linked binary.  
And if you place that single executable on top of scratch, a distroless image, or a small image like alpine, your final image has a minimal footprint which is great for 
consumption and reuse.
