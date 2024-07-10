## Env Setup
### Setup MYSQL
- Install Docker

- Pull the MySQL Docker image:
Open a terminal and run the following command to download the official MySQL image:
```bash
docker pull mysql

- Create and run a MySQL container:
```bash
docker run --name mysql-container -e MYSQL_ROOT_PASSWORD=1234560 -p 3306:3306 -d mysql
```

- Verify the container is running:
```bash
docker ps
```
- Connect to the MySQL server,and create database:
```bash
docker exec -it mysql-container mysql -uroot -padmin
```
- Once connected to MySQL, you can create a new database using:
```
CREATE DATABASE llm
```
- Download script file and setup the database:
```bash
cd initial_data && wget https://github.com/fengxu1211/generative-bi-using-rag/raw/demo_data/application/initial_data/init_mysql_db.sql.zip

unzip init_mysql_db.sql.zip && cd ..

docker exec nlq-mysql sh -c "mysql -u root -p1234560 -D llm  < /opt/data/init_mysql_db.sql"
```


- To stop the container when you're done, use:
```bash
docker stop mysql-container
```

- To remove the container when you're done, use:
```bash
docker rm mysql-container
```

- To start it again later, use:
```bash
docker start mysql-container
```# text2sql_agent
