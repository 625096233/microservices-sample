# microservices-sample
Ratpack based microservices sample with two service:

*  User Service: A simple service for adding and connecting users.
*  Persistence Service: For saving entities and finding them by id.

`User Service` uses `Persistence Service` for persisting users. `Persistence Service` will be discovered automatically,
there is no need to pass details to `User Service` runner.

Current `Persistence Service` implementation uses `Hazelcast` as in-memory, distributed database.


# Building

Build user-service:
```bash
cd user-service
../gradlew shadowJar
```

Build persistence-service:
```bash
cd user-service
../gradlew shadowJar
```

Note: `shadowJar` task will create a single fat jar file in `build/libs` directory.


# Quick test
After building the services, run `user-service` and `persistence-service` in two separate terminals:

User-service:
```bash
cd user-service/build/libs/
java -jar user-service-0.0.1-all.jar
```

Persistence-service:
```bash
cd persistence-service/build/libs/
java -jar persistence-service-0.0.1-all.jar
```
Note:

* `persistence-service` must be run *after* `user-service` to be discovered automatically.

Create first user:
```bash
curl -X POST -H "Content-Type: application/json" -d '{"name": "user1"}' http://localhost:2000/v1/users
# or with httpie
echo '{"name": "user1"}' | http post :2000/v1/users
```

Create second user:
```bash
curl -X POST -H "Content-Type: application/json" -d '{"name": "user2"}' http://localhost:2000/v1/users
#or
echo '{"name": "user2"}' | http post :2000/v1/users
```

Connect user1 to user2:
```bash
curl -X POST http://localhost:2000/v1/users/<user1-id>/connect?to=<user2-id>
#or
http post :2000/v1/users/<user1-id>/connect?to<user2-id>
```

Get users connections:
```bash
curl http://localhost:2000/v1/users/<user1-id>/connections
#or
http :2000/v1/users/<user1-id>/connections
```

See [UserServiceTest](user-service/src/integrationTest/java/microservices/sample/user/UserServiceTest.java) integration test for service usage.

# Issues

*Hazelcast* nodes can not detect each other on two different hosts.


# Modules

* [user-service](user-service): User service implementation
* [persistence-service](persistence-service): Persistence service implementation
* [service-discovery](service-discovery): Service discovery definitions
* [hazelcast-discovery](hazelcast-discovery): Hazelcast-based service discovery implementation
* [base-server](base-server): Helpers and base classes for building Ratpack-based micro-service
* [service-api](service-api): Base classes and REST definitions for clients

# Running tests

Unit tests:

`./gradlew test`

Integration tests:

`./gradlew intT`
