# Projeto contêiner Public Knowledge Project/Open Journal Systems

## Objetivo

Realizar a atualização das versões do [PHP](#o-que-é-php) e do [Open Journal Systems (OJS)](#o-que-é-ojs).

## Descrição

O principal objetivo deste projeto é realizar a atualização das versões do PHP e do OJS. A atualização é necessária para garantir a segurança, estabilidade e compatibilidade com as últimas tecnologias, além de aproveitar os novos recursos e melhorias oferecidos pelas versões mais recentes.

## Escopo

- OJS
- 3.2.1-4
- Apache
- Postgres
- PHP 7.3

### Ferramentas aplicadas

- PHP
- OJS
- Apache
- Postgre
- Docker

#### O que é OJS ?

Open Journal Systems (OJS) é um sistema de gerenciamento e publicação de periódicos que foi desenvolvido pelo [Public Knowledge Project](https://pkp.sfu.ca/) por meio de seus esforços financiados pelo governo federal para expandir e melhorar o acesso à pesquisa. Esta ferramenta contempla ações essenciais à automação das atividades de editoração de periódicos científicos.

#### O que é PHP ?

PHP é uma linguagem de programação de propósito geral amplamente utilizada para o desenvolvimento web. A sigla PHP originalmente significava "Personal Home Page", pois foi criada inicialmente por Rasmus Lerdorf em 1994 para gerenciar seu currículo online. Com o tempo, PHP evoluiu e sua sigla foi redefinida para "PHP: Hypertext Preprocessor", refletindo seu uso principal na geração de conteúdo dinâmico em páginas web.

## Exemplos e Uso

Se você quiser executá-lo localmente (ou em seu próprio servidor), primeiro você precisa instalar
[docker](https://docs.docker.com/get-docker/) (mesmo [docker-compose](https://docs.docker.com/compose/install/) também é recomendado).

Depois disso, observe que para todas as versões disponíveis, fornecemos um arquivo de configuração **docker-compose** para
você poderá iniciar uma pilha OJS completa (aplicativo web + contêineres de banco de dados) em 4 etapas fáceis:

1. Clone este repositório em sua máquina (se preferir, você também pode [baixar](https://github.com/pkp/docker-ojs/archive/master.zip) e descompactá-lo):

     ```bash
     $ git clone https://github.com/pkp/docker-ojs.git
     ```

2. Acesse o diretório da versão do OJS de sua preferência:
     ```bash
     $ versões cd/3_2_0-1/alpine/apache/php
     ```
     | **DICA: Mapeie sua configuração** |
     |:----------------------------------|
     | Nos sites de produção, você gostaria de alterar a configuração padrão. <br /> A maneira recomendada é descomentar as seções de variáveis de ambiente em seu docker-compose.yml e definir as [variáveis de ambiente](#variáveis-​​de-ambiente) corretamente. |
     | Mais informações em ["Maneira fácil de alterar coisas de configuração"](#easy-way-to-change-config-stuff) |

3. Execute a pilha:
     ```bash
     $ docker-compose up
     ```

     O Docker-compose extrairá imagens do dockerHub e fará todo o trabalho duro para você criar uma pilha OJS totalmente funcional.

4. Acesse **http://127.0.0.1:8081** e continue com o processo de instalação web.

     Observe que a conexão com o banco de dados precisa das seguintes opções:

     - **Driver de banco de dados**: `mysqli` (ou "mysql" se seu php for inferior a 7.3)
     - **Host**: `db` (que é o nome do contêiner na rede Docker interna)
     - **Nome de usuário**: `ojs`
     - **Senha**: `ojs`
     - **Nome do banco de dados**: `ojs`
     - _Desmarque_ "Criar novo banco de dados"
     - _Desmarque_ "Beacon"

     E o "Diretório para uploads:" de acordo com seu docker-compose "/var/www/files"

     | **DICA:** |
     |:---------------------|
     | Para passar pelo processo de instalação do OJS automaticamente, defina a variável de ambiente `OJS_CLI_INSTALL=1` e use as outras variáveis .env para automatizar o processo. |

### Demostração

A maneira mais rápida de testar o docker-ojs é executar a pilha em "Play with docker".

Basta clicar no link a seguir e criar um usuário:

[Tente em PWD](https://labs.play-with-docker.com/?stack=https://raw.githubusercontent.com/pkp/docker-ojs/master/versions/3_2_0-2/alpine/apache/php73/docker-compose.yml)

Em seguida, espere até que os contêineres sejam construídos e clique no link da porta 8081 para obter uma demonstração nova e limpa com a última versão estável do OJS.

| **DICA: Versões antigas em PWD ** |
|:----------------------------------|
| Altere o número da versão [neste URL](https://labs.play-with-docker.com/?stack=https://raw.githubusercontent.com/pkp/docker-ojs/master/versions/3_2_0-2/alpine/apache/php73/docker-compose.yml) se quiser testar uma versão mais antiga. |

### Construindo imagens locais

A imagem oficial funcionará para 90% das pessoas, mas, se você não quiser dependências externas ou quiser modificar nossos Dockerfiles oficiais para atender às suas necessidades específicas, você precisará construir suas imagens em sua máquina.

Cada pasta de versão também inclui um arquivo yml alternativo chamado `docker-compose-local.yml`.

Esta composição não solicitará ao dockerHub as imagens necessárias, mas criará uma imagem do docker localmente.

Para fazer isso...

1. Vá para a pasta da sua versão preferida e construa a imagem da seguinte forma:
     ```bash
     $ docker build -t local/ojs:3_2_1-4 .
     ```

     Se algo der errado, verifique se você executou o comando anterior com o número de versão correto ou em uma pasta sem o Dockerfile local.

2. Depois que a imagem for construída, você pode executar a pilha localmente informando ao compose para usar o arquivo yaml local com a opção `-f`/`--file` da seguinte maneira:
     ```bash
     $ docker-compose --file docker-compose-local.yml up
     ```

### Variáveis ​​de ambiente

A imagem compreende as seguintes variáveis de ambiente:

| NOME             | Padrão           | Informações                                                        |
|:----------------:|:----------------:|:-------------------------------------------------------------------|
| NOME DO SERVIDOR | host local       | Usado para gerar httpd.conf e certificado                          |
| OJS_CLI_INSTALL  | 0                | Usado para instalar ojs automaticamente ao iniciar o contêiner     |
| OJS_DB_HOST      | banco de dados   | Host de banco de dados                                             |
| OJS_DB_USER      | ojs              | Banco de dados                                                     |
| OJS_DB_PASSWORD  | ojsPwd           | Senha do banco de dados                                            |
| OJS_DB_NAME      | ojs              | Nome do banco de dados                                             |

_**Observação:** OJS_CLI_INSTALL e recursos de certificado estão em construção._

### Volumes Especiais

O conteúdo do Docker é efêmero por design, mas em algumas situações você gostaria
para manter algumas coisas **persistentes** entre as reinicializações do docker (ou seja: conteúdo do banco de dados,
upload de arquivos, desenvolvimento de plugins...)

Por padrão incluímos uma estrutura de diretórios nas pastas de versão
(veja ./volumes), mas eles estão vazios e desabilitados por padrão em sua composição.
Para habilitá-los, **você só precisa descomentar as linhas de volume em
seu docker-compose.yml** e preencha as pastas corretamente.

Quando você executa o docker-compose ele monta os volumes com persistência
dados e permitirá que você compartilhe arquivos do seu host com o contêiner.

| Host                                    | Container  | Volume                                | Descrição                      |
|:----------------------------------------|:----------:|:--------------------------------------|:-------------------------------|
| ./volumes/public                        | ojs        | /var/www/html/public                  | All public files               |
| ./volumes/private                       | ojs        | /var/www/files                        | All private files (uploads)    |
| ./volumes/config/db.charset.conf        | db         | /etc/mysql/conf.d/charset.cnf         | mariaDB config file            |
| ./volumes/config/ojs.config.inc.php     | ojs        | /var/www/html/config.inc.php          | OJS config file                |
| ./volumes/config/php.custom.ini         | ojs        | /usr/local/etc/php/conf.d/custom.ini  | PHP custom.init                |
| ./volumes/config/apache.htaccess        | ojs        | /var/www/html/.htaccess               | Apache2 htaccess               |
| ./volumes/logs/app                      | ojs        | /var/log/apache2                      | Apache2 Logs                   |
| ./volumes/logs/db                       | db         | /var/log/mysql                        | mariaDB Logs                   |
| ./volumes/db                            | db         | /var/lib/mysql                        | mariaDB database content       |
| ./volumes/migration                     | db         | /docker-entrypoint-initdb.d           | DB init folder (with SQLs)     |
| /etc/localtime                          | ojs        | /etc/localtime                        | Sync clock with the host one.  |
| TBD                                     | ojs        | /etc/ssl/apache2/server.pem           | SSL **crt** certificate        |
| TBD                                     | ojs        | /etc/ssl/apache2/server.key           | SSL **key** certificate        |

Nesta imagem usamos "bind volumes" com caminhos relativos porque isso irá
fornecer uma visão clara de onde seus dados estão armazenados.

A desvantagem desses volumes é que eles não podem ser "nomeados" e o docker irá
armazene-os com um caminho absoluto (é chato tornar as coisas portáteis)
mas prefiro um controle melhor sobre onde os dados são armazenados do que deixá-los nas mãos do docker.

E lembre-se que esta é apenas uma imagem, então fique à vontade para modificá-la para atender às suas necessidades.

Você pode adicionar seus próprios volumes. Por exemplo, faz sentido para um desenvolvedor de plugins
ou um tematizador para criar um volume com seu trabalho, para manter uma cópia persistente em
o host do novo plugin ou tema.

Uma forma alternativa de trabalhar para desenvolvedores é trabalhar com seus próprios desenvolvedores locais.
Dockerfile que será construído para extrair o plugin para seu próprio repositório...
mas isso será significativamente mais lento que o método de volume.

Por último, mas não menos importante, essas pastas de armazenamento precisam existir com as permissões corretas
antes de executar o docker-compose ou ele falhará.

Para ter certeza de que seus volumes têm as permissões corretas, você pode executar estes comandos:

    ```bash
    $ chown 100:101 ./volumes -R
    $ chown 999:999 ./volumes/db -R
    $ chown 999:999 ./volumes/logs/db -R
    ```

Em outras palavras... todo o conteúdo dentro dos volumes será de propriedade do usuário apache2
e grupo (uid 100 e gid 101 dentro do contêiner), execpt para db e logs/db
pastas que pertencerão ao usuário e grupo mysql (uid e gid 999).

## Scripts integrados

O Dockerfile inclui alguns scripts em "/usr/local/bin" para facilitar operações comuns:

| Script               | Container  | Descrição                                                                                                       |
|:---------------------|:----------:|:----------------------------------------------------------------------------------------------------------------|
| ojs-run-scheduled    | ojs        | Executa "ferramentas php/runScheduledTasks.php". Chamado pelo cron a cada hora.                                 |
| ojs-cli-install      | ojs        | Usa curl para chamar a instalação do ojs usando variáveis ​​predefinidas.                                         |
| ojs-pre-start        | ojs        | Aplica algumas variáveis de configuração e gera um certificado autoassinado baseado em ServerName.              |
| ojs-upgrade          | ojs        | Executa "php tools/upgrade.php upgrade". (problema quando config.inc.php é um volume)                           |
| ojs-variable         | ojs        | Substitui o valor da variável em config.inc.php (ou seja: variável ojs-variable newValue)                       |
| ojs-migrate          | ojs        | Pega um dump.sql, arquivos públicos e privados da pasta "migration" e constrói e constrói um site docker (beta) |

Alguns desses scripts ainda são beta, tome cuidado ao usá-los.

Você pode chamar os scripts fora do contêiner da seguinte maneira:

    ```bash
    $ docker exec -it ojs_app_journalname /usr/local/bin/ojs-variable session_check_ip Desativado
    ```
## Atualizando OJS

Graças ao docker, o processo de atualização é fácil e direto.

1. **Pare a pilha** com a versão antiga do OJS (por exemplo "pkpofficial/ojs:2_4_8-5").
    ```bash
    $ parada de composição do docker
    ```
2. **Defina a nova versão** em docker-compose.yml.

      Substitua a versão antiga: ```image: pkpofficial/ojs:2_4_5-2```

      com o novo: ```image: pkpofficial/ojs:3_2_1-4```

3. **Inicie o contêiner**. Conforme você altera a versão, o docker-compose irá extrair automaticamente uma nova imagem com uma versão atualizada do código OJS que substituirá a antiga (lembre-se de que os contêineres não são persistentes).
    ```bash
    $ docker-compose up
    ```
4. **Execute o script de atualização** para atualizar o banco de dados e os arquivos OJS. O mais fácil foi conectar-se ao seu contêiner OJS com [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/) e executar o script integrado `ojs-upgrade` com esta única linha :
    ```bash
    $ docker exec -it ojs_app_journalname /usr/local/bin/ojs-upgrade
    ```
    | **DICA:** Descubra o nome do seu contêiner? |
    |:------------------------------------------|
    | Você pode ver o nome de todos os seus contêineres com `docker ps -a`. Os relacionados ao OJS serão algo como `ojs_app_journalname`. |
    | Use grep para filtrar da seguinte forma: `$ docker ps -a | grep ojs_app` |

Antes da atualização você gostaria de [diff](https://linux.die.net/man/1/diff) seu `config.inc.php` com a versão da nova versão do OJS para aprender sobre novas variáveis de configuração. Tenha especial cuidado com os charsets.

### Compatibilidade de versões OJS com PHP 

Diferentes versões do OJS são combinadas com diferentes versões do PHP (5 a 8...).

|OJS   |PHP        |
|------|-----------|
|3.1.2 |>=7.0      |
|3.2.1 |>=7.2      |
|3.3   |>=7.3      |
|3.4   |>=8.0      |

### Versões PHP 

|VERSÕES|ULTIMA VERSÃO |STATUS DA VERSÃO                    |VIDA ÚTIL     |DATA DE LANÇAMENTO|TERMINO DE SUPORTE|DATA DE OBSOLESCÊNCIA|
|-------|--------------|------------------------------------|--------------|------------------|------------------|---------------------|
|7.0    |Não Informado |Não compatível                      |Atingida      |03/12/2015        |10/01/2019        |Não informado        | 
|7.1    |Não Informado |Não compatível                      |Atingida      |01/12/2016        |01/12/2019        |Não informado        |
|7.2    |7.2.34        |Não compatível                      |Atingida      |30/11/2017        |30/11/2019        |30/11/2020           |
|7.3    |7.3.33        |Não compatível                      |Atingida      |06/12/2018        |06/12/2020        |06/12/2021           |
|7.4    |7.4.33        |Não compatível                      |Atingida      |28/11/2019        |28/11/2021        |28/11/2022           |
|8.0    |8.0.30        |Não compatível                      |Atingida      |26/11/2020        |26/11/2022        |26/11/2023           |
|8.1    |8.1.27        |Somente correções de segurança      |Não atingida  |25/11/2021        |25/11/2023        |25/11/2024           |    
|8.2    |8.2.15        |Suportado                           |Não atingida  |08/12/2022        |08/12/2024        |08/12/2025           |
|8.3    |8.3.2         |Suportado (Versão mais recente)     |Não atingida  |23/11/2023        |23/11/2025        |23/11/2026           |
|8.4    |Não Lançado   |Não Lançado                         |Não Lançado   |Prevista para 2024|Não Lançado       |Não Lançado          |

_**Observação:** PHP 8.1 não recomendado para OJS <3.4, devido a problema com plugin de terceiros._

| **AVISO:** Posso atualizar diretamente para a última versão estável do OJS?                                              |
|:-------------------------------------------------------------------------------------------------------------------------|
| Depende da sua versão inicial. A rota de atualização recomendada é:<br/> **2.x > 2.4.8-5 > 3.1.2-4 > 3.2.1-4 > 3.3.x-x** |

## Registro de Mudanças


## Contribuidores


## Convensões adotadas

- Semver
- Keep a Changelog 
- Versionamento Semântico
- GIT Conventional Commits

## Tecnologias adotadas


## Licenças de Software


# Considerações Finais

O branch docker-ojs serve como uma base sólida para qualquer desenvolvimento ou personalização no ambiente OJS. Manter esse branch atualizado é fundamental para garantir que qualquer alteração posterior não comprometa as configurações originais. Certifique-se de documentar qualquer mudança significativa realizada neste branch para facilitar a colaboração e a manutenção futuras.
=======
# OJS (Open Journal Systems) - PKP - Container/Docker

| **IMPORTANT:** |
|:---------------------------------------------------------|
| We have temporarily moved to gitLab to test the CI/CD tools so the latest version of this repository is now available here: https://gitlab.com/pkp-org/docker |
| This is a temporary change as most likely in the near future, we will move back to this same repository and use gitHubActions. |
| Sorry for the inconvenience. |


**This repository is still beta, so it should be used with care in production settings - please provide feedback early and often about your experience.** <br />We are actively working to release a stable version soon. Keep tuned.

Open Journal Systems (OJS) is a journal management and publishing system that has been developed by the [Public Knowledge Project](https://pkp.sfu.ca/) through its federally funded efforts to expand and improve access to research.

The images in this repository are built on top of [Alpine Linux](https://alpinelinux.org/) and come in several variants (see [Versions](#versions)).

This repository is a fork of the work formerly done by [Lucas Dietrich](https://github.com/lucasdiedrich/ojs).


<!-- ## Demo

The fastest way to test docker-ojs is running the stack over "Play with docker".

Just click the following link and create a user:

[![Try in PWD](https://raw.githubusercontent.com/play-with-docker/stacks/master/assets/images/button.png)](https://labs.play-with-docker.com/?stack=https://raw.githubusercontent.com/pkp/docker-ojs/master/versions/3_2_0-2/alpine/apache/php73/docker-compose.yml)

Then wait till the containers are built and click in the 8081 port link to get a fresh clean demo with the last stable ojs version.

| **TIP: Old versions on PWD **                                             |
|:--------------------------------------------------------------------------|
| Change the version number in [this url](https://labs.play-with-docker.com/?stack=https://raw.githubusercontent.com/pkp/docker-ojs/master/versions/3_2_0-2/alpine/apache/php73/docker-compose.yml) if you want to test an older version. |
-->

## How to use

If you want to run it locally (or in your own server), first you need to install
[docker](https://docs.docker.com/get-docker/) (even [docker-compose](https://docs.docker.com/compose/install/) it's also recommended).

You can have it all up and running in less than 10 minutes following this brief howto:
https://www.digitalocean.com/community/tutorials/how-to-install-docker-compose-on-debian-10

After this, notice that for all available versions, we provide a **docker-compose** configuration file so
you will be able to start a full OJS stack (web app + database containers) in 4 easy steps:

1. Clone this repository in your machine (if youprefer, you can also [download](https://github.com/pkp/docker-ojs/archive/master.zip) and unzip it):

    ```bash
    $ git clone https://github.com/pkp/docker-ojs.git
    ```

2. Go to the directory of your OJS version of your choice:
    ```bash
    $ cd versions/3_2_0-1/alpine/apache/php
    ```
    | **TIP: Map your config** |
    |:-----------------------------------------------------------------------------------|
    | In production sites you would like to change the default configuration. <br /> The recommended way is uncommenting the environment variable sections in your docker-compose.yml and set the [environment variables](#environment-variables) properly. |
    | More info at ["Easy way to change config stuff"](#easy-way-to-change-config-stuff) |

3. Run the stack:
    ```bash
    $ docker-compose up
    ```

    Docker-compose will pull images from dockerHub and do all the hard work for you to rise a full functional OJS stack.

4. Access **http://127.0.0.1:8081** and continue through web installation process.

    Note that the database connection needs the following options:

    - **Database driver**: `mysqli` (or "mysql" if your php is lower than 7.3)
    - **Host**: `db` (which is the name of the container in the internal Docker network)
    - **Username**: `ojs`
    - **Password**: `ojs`
    - **Database name**: `ojs`
    - _Uncheck_ "Create new database"
    - _Uncheck_ "Beacon"

    And the  "Directory for uploads:" acording to your docker-compose "/var/www/files"

    | **TIP:**             |
    |:---------------------|
    | To go through the OJS installation process automatically, set the environment variable `OJS_CLI_INSTALL=1`, and use the other .env variables to automatize the process. |

That's all. Easy peasy, isn't it?

Ok, let's talk talk about more complex concepts and scenarios.

## Building local images

The official image will work for 90% of the people but, if you don't want external dependencies or you like to modify our official Dockerfiles to fit your specific needs you will need to build your images in your machine.

Each version folder also includes an alternative yml file called `docker-compose-local.yml`.

This compose won't ask dockerHub for the required images, it will build a docker image locally.

To do this...

1. Go to your preferred version folder and and build the image as follows:
    ```bash
    $ docker build -t local/ojs:3_2_1-4 .
    ```

    If something goes wrong, double-check if you ran the former command with the right version number or in a folder without the local Dockerfile.

2. Once the image is built, you can run the stack locally telling compose to use the local yaml file with the `-f`/`--file` option as follows:
    ```bash
    $ docker-compose --file docker-compose-local.yml up
    ```

## Versions

Different OJS versions are combined with different versions of PHP (5 and 7), and different web servers ([Apache HTTP Server](https://httpd.apache.org/), [nginx](https://nginx.org/)).

_Currently, not all these combinations work! We are mostly focused in Apache2. PR are welcome_

All version tags can be found at [Docker Hub Tags tab](https://hub.docker.com/r/pkpofficial/ojs/tags/).

(If no webserver is mentioned in the tag, then Apache is used).


## Environment Variables

The image understand the following environment variables:

| NAME            | Default   | Info                 |
|:---------------:|:---------:|:---------------------|
| SERVERNAME      | localhost | Used to generate httpd.conf and certificate            |
| OJS_CLI_INSTALL | 0         | Used to install ojs automatically when start container |
| OJS_DB_HOST     | db        | Database host        |
| OJS_DB_USER     | ojs       | Database             |
| OJS_DB_PASSWORD | ojsPwd    | Database password    |
| OJS_DB_NAME     | ojs       | Database name        |

_**Note:** OJS_CLI_INSTALL and certificate features are under construction._

## Special Volumes

Docker content is efimerous by design, but in some situations you would like
to keep some stuff **persistent** between docker restarts (ie: database content,
upload files, plugin development...)

By default we include an structure of directories in the version folders
(see ./volumes), but they are empty and disabled by default in your compose.
To enable them, **you only need to uncomment the volume lines in
your docker-compose.yml** and fill the folders properly.

When you run the docker-compose it will mount the volumes with persistent
data and will let you share files from your host with the container.


| Host                                    | Container  | Volume                                | Description                    |
|:----------------------------------------|:----------:|:--------------------------------------|:-------------------------------|
| ./volumes/public                        | ojs        | /var/www/html/public                  | All public files               |
| ./volumes/private                       | ojs        | /var/www/files                        | All private files (uploads)    |
| ./volumes/config/db.charset.conf        | db         | /etc/mysql/conf.d/charset.cnf         | mariaDB config file            |
| ./volumes/config/ojs.config.inc.php     | ojs        | /var/www/html/config.inc.php          | OJS config file                |
| ./volumes/config/php.custom.ini         | ojs        | /usr/local/etc/php/conf.d/custom.ini  | PHP custom.init                |
| ./volumes/config/apache.htaccess        | ojs        | /var/www/html/.htaccess               | Apache2 htaccess               |
| ./volumes/logs/app                      | ojs        | /var/log/apache2                      | Apache2 Logs                   |
| ./volumes/logs/db                       | db         | /var/log/mysql                        | mariaDB Logs                   |
| ./volumes/db                            | db         | /var/lib/mysql                        | mariaDB database content       |
| ./volumes/migration                     | db         | /docker-entrypoint-initdb.d           | DB init folder (with SQLs)     |
| /etc/localtime                          | ojs        | /etc/localtime                        | Sync clock with the host one.  |
| TBD                                     | ojs        | /etc/ssl/apache2/server.pem           | SSL **crt** certificate        |
| TBD                                     | ojs        | /etc/ssl/apache2/server.key           | SSL **key** certificate        |

In this image we use "bind volumes" with relative paths because it will 
give you a clear view where your data is stored.

The down sides of those volumes is they can not be "named" and docker will 
store them with an absolute path (that it’s annoying to make stuff portable) 
but I prefer better control about where data is stored than leave it in docker hands.

And remember this is just an image, so feel free to modify to fit your needs.

You can add your own volumes. For instance, make sense for a plugin developer
or a themer to create a volume with his/her work, to keep a persistent copy in
the host of the new plugin or theme.

An alternative way of working for developers is working with his/her own local
Dockerfile that will be build to pull the plugin for his/her own repository...
but this will be significantly slower than the volume method.

Last but not least, those storage folders need to exist with the right permissions
before you run your docker-compose or it will fail.

To be sure your volumes have the right permissions, you can run those commands:

   ```bash
   $ chown 100:101 ./volumes -R
   $ chown 999:999 ./volumes/db -R
   $ chown 999:999 ./volumes/logs/db -R
   ```

In other words... all the content inside volumes will be owned by apache2 user
and group (uid 100 and gid 101 inside the container), execpt for db and logs/db
folders that will be owned by mysql user and group (uid and gid 999).


## Built in scripts

The Dockerfile includes some scritps at "/usr/local/bin" to facilitate common opperations:

| Script               | Container  | Description                                                                                                   |
|:---------------------|:----------:|:--------------------------------------------------------------------------------------------------------------|
| ojs-run-scheduled    | ojs        | Runs "php tools/runScheduledTasks.php". Called by cron every hour.                                            |
| ojs-cli-install      | ojs        | Uses curl to call the ojs install using pre-defined variables.                                                |
| ojs-pre-start        | ojs        | Enforces some config variables and generates a self-signed cert based on ServerName.                          |
| ojs-upgrade          | ojs        | Runs "php tools/upgrade.php upgrade". (issue when config.inc.php is a volume)                                 |
| ojs-variable         | ojs        | Replaces the variable value in config.inc.php (ie: ojs-variable variable newValue)                            |
| ojs-migrate          | ojs        | Takes a dump.sql, public and private files from "migration" folder and builds and builds a docker site (beta) |

Some of those scripts are still beta, you be careful when you use them.

You can call the scripts outside the container as follows:

   ```bash
   $ docker exec -it ojs_app_journalname /usr/local/bin/ojs-variable session_check_ip Off
   ```

## Upgrading OJS

Thanks to docker, the ugrade process is easy and straightforward.

1. **Stop the stack** with the old OJS version (for instance "pkpofficial/ojs:2_4_8-5").
   ```bash
   $ docker-compose stop
   ```
2. **Set the new version** in docker-compose.yml.

     Replace the old version: ```image: pkpofficial/ojs:2_4_5-2```

     with the new one:        ```image: pkpofficial/ojs:3_2_1-4```

3. **Start the container**. As you changed the version, docker-compose will automatically pull a new image with an updated version of the OJS code that will replace the old one (remember that containers are not persistent).
   ```bash
   $ docker-compose up
   ```
4. **Run the upgrade script** to upgrade the OJS database and files. Easiest was is connecting to your OJS container with [`docker exec`](https://docs.docker.com/engine/reference/commandline/exec/) and run the `ojs-upgrade` built in script with this single line:
   ```bash
   $ docker exec -it ojs_app_journalname /usr/local/bin/ojs-upgrade
   ```
   | **TIP:** Discover your container name? |
   |:---------------------------------------|
   | You can see the name of all your containers with `docker ps -a`. The ones related with OJS will be something like `ojs_app_journalname`. |
   | Use grep to filter as follows: `$ docker ps -a | grep ojs_app` |

Before the upgrade you will like to [diff](https://linux.die.net/man/1/diff) your `config.inc.php` with the version of the new OJS version to learn about new configuration variables. Be specially carefully with the charsets.

   | **WARNING:** May I upgrade directly to the last OJS stable version?                                             |
   |:----------------------------------------------------------------------------------------------------------------|
   | It depends on your initial version. The recommended upgrade route is:<br/> **2.x > 2.4.8-5 > 3.1.2-4 > 3.2.1-4 > 3.3.x-x** |


## Apache2

As said, right now the only avaliable stack is Apache2, so configuration files
and volumes are thought you will work over an apache.

If you want to know the fastest method to set your own config jump to the next
section **["Easy way to change config stuff"](#easy-way-to-change-config-stuff)**.

For those who like to understand what happens behind the courtains, you need to
know that during the image building we ask the Dockerfile to copy the content of
`./templates/webServers/apache/phpVersion/root` folder in the root
of your container.

It means, if you like to add your specific php settings you can do it
creating a PHP configuration in `./templates/webServers/php{5,7,73}/conf.d/0-ojs.ini`
and build you own image locally.

There are some optimized variables already, you can check them within each
version directory, e.g. `/root/etc/apache2/conf.d/ojs.conf` with your virtualhost
or `/etc/supervisor.conf`... or, now you know what Dockerfile will do, you
can add your own.

**Again:** Note that template's /root folder is copied into the Docker image
**at build time** so, if you change it, you must rebuild the image for changes
to take effect.

### Easy way to change config stuff

If you don't want to keep you own local images, you can use the ones we
build in dockerHub and map your config files in your docker-compose.yml.

So if you like to change something in (for instance) your php settings,
you only need to created create a `./volumes/config/php.custom.ini`
outside your container and uncomment the volume in your docker-compose.yml.

Check the volumes section for a list of folders and files we think could
be useful to overwrite or extend to fit your needs.

### Restful URLs (aka. clean URLs)

By default the restful_url are enabled and Apache is already configured,
so there is no need to use index.php over url.

### SSL

By default at the start of Apache one script will check if the SSL certificate
is valid and its CN matches your SERVERNAME, if don't it will generate a new one.
The certificate can be overwritten using a volume mount (see `docker-compose.yml` file).

_**Note:** This feature is under reveiw and could change in future._

### SSL handled by an external service

If you have an external service in front handling SSL connections (often referred as
*SSL offloading* or *SSL termination*) you need to add a new line containing
`PassENV HTTPS` in `ojs.conf`, inside the main `<VirtualHost *:80>` section.

# How could I help?

## Update the compose configurations and Dockerfiles

If you want to join the docker-ojs (aka. docker4ojs) team you will like to
contribute with new Dockerfiles or docker-composes. To do this, you will need
to clone this project and send us a PR with your contributions.

Before we follow, let us clarify something:
Versions of this project fit with OJS tag versions, BUT...
if you take a look to PKP's repositories, you will notice the tag naming
syntax changed at version 3.1.2-0. We moved from "ojs-3_1_2-0" to "3_1_2-0".

So, to avoid keeping in mind at what momment we changed the syntax, in this
project we only use the modern syntax, so you cant/must refer every version
with the same notation.

Said this, let's talk about how we generate all this versions:

The files for the different OJS versions and software stacks are generated based
on a number of template files, see directory `templates`.

You can re-generate all `Dockerfile`, `docker-compose(-local).yml`, configuration
files, etc. calling the `build.sh` script.

```bash
# generate a specific version
$ ./build.sh 3_1_2-4

# generate _all_ versions
$ ./build.sh
```

## Add a new stack to the list

Let's say we like us to distribute a "nginx" stack. We have our hands full but
you know docker and nginx so... ¿how could you contribute?

1. Be sure the version number is in the versions.list file (ie: 3_2_1-0).
2. Create the required files and folders in the templates folder.

   Basically you will need:

   - A Dockerfile template for each php version you publish (ie: dockerfile-alpine-nginx-php73.template)
   - A generic docker-compose.yml template (templates/dockerComposes/docker-compose-nginx.template)
   - A folder with all the specific configurations (templates/webServers/nginx/php73/root)
   - Extend exclude.list with the stuff you want to be removed.

   This is the hard work. Take apache as a reference and contact us if you need indications.

3. Edit build.sh to add your version to the proper variables.

   For the nginx exemple (over alpine) it should be enough extending the webServers array:
   ```
   webServers=(  'apache' 'nginx' )
   ```
   Modify the script if you need it to be smarter.

4. Run build script to generate all versions again:

   ```bash
   $ ./build.sh
   ```

5. Test your work running docker-compose with local dockerfiles.

6. Commit a PR with your new build.sh and templates (ignore the versions folder).


## License

GPL3 © [PKP](https://github.com/pkp)
