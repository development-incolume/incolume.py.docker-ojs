## escopo

- alpine 3.19
- OJS 3.3.0-15
- Apache
- Postgresql
- PHP81


Construir imagem passando proxy do ambiente
```shell
docker build --build-arg HTTP_PROXY="10.1.101.101:8080" --build-arg HTTPS_PROXY="10.1.101.101:8080" --build-arg https_proxy="10.1.101.101:8080" --build-arg http_proxy="10.1.101.101:8080" -t incolume/rjp:0.2.0 .
```
