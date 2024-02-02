## escopo

- alpine 3.18
- OJS 3.4.0-1
- Apache
- Postgresql
- PHP 8.1.26


Construir imagem passando proxy do ambiente
```shell
docker build --build-arg HTTP_PROXY="10.1.101.101:8080" --build-arg HTTPS_PROXY="10.1.101.101:8080" --build-arg https_proxy="10.1.101.101:8080" --build-arg http_proxy="10.1.101.101:8080" -t incolume/rjp:0.2.0 .
```
