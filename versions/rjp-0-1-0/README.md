## escopo

- alpine 3.11
- OJS 3.2.1-4
- Apache
- Postgresql
- PHP7


Construir imagem passando proxy do ambiente
```shell
docker build --build-arg HTTP_PROXY="10.1.101.101:8080" --build-arg HTTPS_PROXY="10.1.101.101:8080" --build-arg https_proxy="10.1.101.101:8080" --build-arg http_proxy="10.1.101.101:8080" .
```
