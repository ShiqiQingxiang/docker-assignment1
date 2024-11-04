## Agile Software Practice - Assignment 1.





The app’s architecture is illustrated below:

<img src="C:\Users\shiqiqingxiang\AppData\Roaming\Typora\typora-user-images\image-20241104043055093.png" alt="image-20241104043055093" style="zoom: 50%;" />

- The application contains the following main components:
  - **Movies API**: `doconnor/movies-api:1.0`
  - **MongoDB**: `mongo:8.0-rc`
  - **Redis**: `redis:alpine`
  - **Mongo Express**: `mongo-express:1.0-20-alpine3.19`



### Network Isolation

**3 network namespace:**

1. movies-network (Movies API and Redis)
2. mongo-network (Movies API and Mongodb and mongo-seed)
3. admin-network (Mongo Express and Mongodb)



### Seeding

```yml
 mongo-seed:
    image: mongo:8.0-rc
    depends_on:
      - mongodb
    command: /mongo-seed.sh
    volumes:
      - ./seeding.json:/seeding.json
      - ./mongo-seed.sh:/mongo-seed.sh
    profiles:
      - dev
    environment:
      - MONGODB_DATABASE=${MONGODB_DATABASE}
      - MONGODB_URI=mongodb://${MONGODB_USERNAME}:${MONGODB_PASSWORD}@mongodb:27017
    networks:
      - mongo-network
```

Use the mongoimport tool to import data from a json file into a database.下 bash。`seeding-mongo.sh`

```bash
mongoimport --host mongodb --username admin --password password --db Movies --collection movies --type json --file seeding.json --jsonArray --authenticationDatabase=admin
```

### Dev 模式

use for control  `profiles`

For example, Dev service:	`docker-compose -f .\docker-compose.yml --profile dev up -d`

​			Normal service:	`docker-compose -f .\docker-compose.yml up -d`

#### reference:

 https://levelup.gitconnected.com/dockerising-your-database-for-local-development-seeding-data-mariadb-sql-and-mongodb-nosql-f18ba6d8c9ed	(The way seeding database)

