## Route 53
DNS сервер

## EC2 Elastic Cloud Compute
Дозволяє запустити віртуальний сервер. Можна вибрати операційну систему та потрібні аплікейшини

## EBS Elastick Block storage
Сторедж який атачиться до EC2.

## ECS Elastic Container Service
Дозволяє деплоїти і менеджети Docker контейнери. Також застосовується з тулзою Fagrante

## Amazon RDS (Relational Database system)
Сервіс для реляційних баз даних.  
Підтримує MySQL, SQL Server, Oracle, PostgreSQL and MariaDB.  
Multi-AZ.

## VPC Virtual Private Cloud

## DynamoDB
Документо-орієнтована база даних.
Має таблиці що є колекцією `items`, а айтеми в свою чергу є колекцією `attributes`.
Кожен айтем повинен мати `primary key`. Це може бути одне поле `partition key` або комбінація двої полів `partition key` + `sort key`.
З CAP теореми DynamoDB вибирає Avilability i Partition Tolerance