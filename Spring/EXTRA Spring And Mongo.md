
NoSQL DB are mechanism for storage and retrieval of data stored in a format different than tables and relationship. They prioritize _availability_ as opposed to _consistency_ (CAP Theorem).
Types of nosql db:

* Key value stores: everything is stored in a hash table linking a unique key to a pointer of data (Redis)
* Clulmn family stores: data is organized in columns(which can have different length and contains heterogeneus data), multiple columns are in a Column Family, They are used for big data (Cassandra)
* Document database: similar to key value stores but based on versioned documents of collections of key-value pairs (like JSON), MongoDB, CouchBase.
* Graph database: a flexible graph model is used to store data: Neo4j.

How to integrate Mongo DB in a spring application:

* install mongoDB (or use a remote instance)
* import spring-data-mongodb, mongodb-driver-core, mongodb-driver-sync
* Tag your DAO objects with @**Document** at class level and @**Id** over the primary key
* Create your repository interfaces extending **MongoRepository<Type, KeyType>**
* create the proper config enabling the mongo repository, declaring the mongo factory bean and the mongo template bean:

```
@Configuration
@EnableMongoRepositories(basePackage = {"com.example.repo"})
public class MongoConfig {
    @Bean
    public MongoDbFactory mongoDbFactory() {
        return new SimpleMongoDbFactory(new MongoClient(host, port), dbName);  //host, port, and db name are the properties of the DB
    }

    @Bean
    public MongoTemplate mongoTemplate() {
        return new MongoTemplate(mongoDbFactory());
    }
}

//otherwise you can extend the AbstractMongoConfiguration and also add the transaction management if needed

@Configuration
@EnableTransactionManagement
@EnableMongoRepositories
public class MongoConfig extends AbstractMongoConfiguration {

    @Bean
    PlatformTransactionManager transactionManager(MongoDbFactory dbFactory) {
        return new MongoTransactionManager(dbFactory);
    }

    @Override
    protected void configureClientSettings(MongoClientSettings.Builder builder) {
        builder.applyConnectionString(new ConnectionString(connectionString));
    }

    @Override
    public String getDatabaseName() {
        return dbName;
    }
}
```

**SPRING BOOT**
include the spring-boot-starter-data-mongodb module and specify the DB details using the application.yml
```
spring:
    data:
        host: 127.4.6.8
        port: 12345
```
and add the _MongoTransactionManager_ in the _Application_ class:
```
@SpringBootApplication
public class Application {
    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }

    @Bean
    PlatformTransactionManager transactionManager(MongoDbFactory dbFactory) {
        return new MongoTransactionManager(daFactory);
    }
}


```
