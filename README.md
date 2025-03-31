# JPA & Hibernate Guide

## Overview
This repository provides a comprehensive guide on Java Persistence API (JPA) and Hibernate, including transaction management, entity lifecycle, relationships, querying, and best practices.

## Table of Contents
1. [Entity Manager and Transactions](#entity-manager-and-transactions)
2. [Entity Lifecycle](#entity-lifecycle)
3. [Relationships in JPA](#relationships-in-jpa)
4. [Querying Data](#querying-data)
5. [Spring Boot Integration](#spring-boot-integration)
6. [Hibernate Behavior and Performance Optimization](#hibernate-behavior-and-performance-optimization)
7. [Best Practices](#best-practices)

---

## Entity Manager and Transactions

```java
entityManager.getTransaction().begin();
entityManager.persist(someEntity);
entityManager.getTransaction().commit();
entityManager.clear();
```

### EntityManager Methods:
- `persist()` - Makes an entity managed and persistent.
- `find()` - Finds an entity by primary key.
- `merge()` - Merges a detached entity into the persistence context.
- `remove()` - Removes an entity from the database.
- `detach()` - Detaches an entity, preventing further automatic updates.
- `clear()` - Clears the persistence context.

---

## Entity Lifecycle

```
New ---> persist() ---> Managed
Managed ---> remove() ---> Removed
Managed ---> detach()/clear()/close() ---> Detached
Detached ---> merge() ---> Managed
```

### Managed vs Detached State
- Managed entities are tracked, and changes are automatically persisted.
- Detached entities are not tracked and require `merge()` to be reattached.

---

## Relationships in JPA

### One-to-One:
```java
@OneToOne(cascade = CascadeType.ALL, fetch = FetchType.LAZY, orphanRemoval = true)
private AvatarImage avatarImage;
```

### One-to-Many:
```java
@OneToMany(mappedBy="foo", orphanRemoval=true)
private List<Bikes> bikes;
```

### Many-to-Many:
```java
@ManyToMany(cascade = { CascadeType.MERGE, CascadeType.PERSIST })
@JoinTable(name = "MATCH_TEAM",
  joinColumns = {@JoinColumn(name = "MATCH_ID")},
  inverseJoinColumns = {@JoinColumn(name = "TEAM_ID")})
private List<Team> teams;
```

---

## Querying Data

### JPQL:
```java
TypedQuery<Person> query = entityManager.createQuery("SELECT p FROM Person p", Person.class);
List<Person> persons = query.getResultList();
```

### Named Queries:
```java
@NamedQuery(name="find_all_persons", query="SELECT p FROM Person p")
```

### Native Queries:
```java
Query nativeQuery = entityManager.createNativeQuery("SELECT * FROM person", Person.class);
List<Person> persons = nativeQuery.getResultList();
```

### JDBC Template:
```java
Person person = jdbcTemplate.queryForObject("SELECT * FROM person WHERE id=?", new Object[]{12}, new BeanPropertyRowMapper<>(Person.class));
```

---

## Spring Boot Integration

### Initialize Schema and Data:
```
resources/schema.sql
resources/data.sql
```

### Enable H2 Console:
```
spring.h2.console.enabled=true
```

### Logging SQL Queries:
```
spring.jpa.show-sql=true
logging.level.org.hibernate.type=trace
spring.jpa.properties.hibernate.format_sql=true
```

---

## Hibernate Behavior and Performance Optimization

### Flushing Data:
```java
entityManager.flush();
```

### Detaching Entities:
```java
entityManager.detach(entity);
```

### Refreshing Entities:
```java
entityManager.refresh(entity);
```

---

## Best Practices

1. **Use `@Transactional` for database operations** to maintain consistency.
2. **Prefer `FetchType.LAZY`** for large associations to optimize performance.
3. **Use `@NamedQuery` for reusable queries** to avoid SQL injection risks.
4. **Enable logging during development** to debug queries.
5. **Avoid `CascadeType.ALL` on relationships** unless absolutely necessary.
6. **Use batch processing for large inserts and updates** to enhance performance.

---

## References
- [Hibernate Documentation](https://hibernate.org/documentation/)
- [JPA Specification](https://javaee.github.io/javaee-spec/javadocs/javax/persistence/package-summary.html)

