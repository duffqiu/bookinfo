
## Introdction
It is a modification version of "ratings mysql" which will
1. connect to Alibaba Cloud RDS-MySQL
2. using ssl connection
3. change database from `test` to `ratings` because `test` is not a vaild name in Alibaba Cloud RDS-MySQL 

## How to build the image

```bash
docker build . -t <your registry address>/<namespace>/ratings:ssl --build-arg service_version=v2
```

if you don't have your own registry, you can create one in Alibaba Cloud:
https://www.alibabacloud.com/product/container-registry


## How to deploy the ratings ssl

1. create a RDS-MySQL instance in Alibaba Cloud and create an account user/password and turn on SSL
2. run the init script in src/mysql/mysqldb-ssl-init.sql
3. grant the account to the database
4. set the correct value in bookinfo-ratings-v2-mysql-ssl.yaml marked with "<>"
5. deploy the ratings-ssl deployment
6. configure istio (depend on how to demo it, not part of this document)

## How to test ssl

1. modify the user only can use ssl to connect mysql
```sql
ALTER USER '<username>'@'%' REQUIRE SSL 
```

2. after access the ratings-ssl service , query the data from mysql
```sql
SELECT t.PROCESSLIST_ID, s.VARIABLE_NAME, s.VARIABLE_VALUE
FROM performance_schema.status_by_thread AS s
JOIN performance_schema.threads AS t ON s.THREAD_ID = t.THREAD_ID
WHERE s.VARIABLE_NAME IN ('Ssl_version', 'Ssl_cipher')
```

and then you can the ssl connections with TLS1.2
