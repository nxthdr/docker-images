# Bird

This Dockerfile is heavilly inspired by https://github.com/akafeng/docker-bird (Apache License 2.0). 
Thanks @akafeng!

The biggest difference is the `BIRD_PROTOCOLS` arg which allow to define which protocols to use. 
You can use `all` as a default, but in this case alpha-released protocols like `bmp` won't be compiled.