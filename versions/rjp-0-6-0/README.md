## escopo

- alpine 3.18
- Apache 2.4.58
- PHP 8.1.26
- OJS 3.4.0-3
- Postgresql / Mysql


Construir imagem passando proxy do ambiente
```shell
docker build --build-arg HTTP_PROXY="10.1.101.101:8080" --build-arg HTTPS_PROXY="10.1.101.101:8080" --build-arg https_proxy="10.1.101.101:8080" --build-arg http_proxy="10.1.101.101:8080" -t incolume/rjp:0.6.0 .
```
