# Projeto rjp
**contêiner Public Knowledge Project/Open Journal Systems para Revista Jurídica da Presidência**

## Objetivo

Realizar a atualização das versões do [PHP](#o-que-é-php) e do [Open Journal Systems (OJS)](#o-que-é-ojs).

## Descrição

O principal objetivo deste projeto é realizar a atualização das versões do PHP e do OJS. A atualização é necessária para garantir a segurança, estabilidade e compatibilidade com as últimas tecnologias, além de aproveitar os novos recursos e melhorias oferecidos pelas versões mais recentes.

## Escopo

- OJS 3.2.1-4
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
