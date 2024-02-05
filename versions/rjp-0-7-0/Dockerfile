ARG OJS_VERSION=3_4_0-4
FROM pkpofficial/ojs:${OJS_VERSION}
LABEL authors="brito"
RUN apk add --no-cache php81-pdo_pgsql  php81-pgsql
CMD ["ojs-start"]
