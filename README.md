<!DOCTYPE html>
<html>
<head>
    <title>JPA + Hibernate Cheat Sheet</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
            max-width: 900px;
            margin: auto;
        }
        code {
            background-color: #f4f4f4;
            padding: 2px 5px;
            border-radius: 4px;
        }
        pre {
            background-color: #f4f4f4;
            padding: 10px;
            border-radius: 4px;
            overflow-x: auto;
        }
    </style>
</head>
<body>
    <h1>JPA (Java Persistence API) + Hibernate Cheat Sheet</h1>

    <h2>Transaction Management</h2>
    <pre>
entityManager.getTransaction().begin();
entityManager.persist(&lt;some-entity&gt;);
entityManager.getTransaction().commit();
entityManager.clear();
    </pre>
    
    <h2>Entity Lifecycle</h2>
    <ul>
        <li><strong>New</strong> → <code>em.persist</code> → Managed</li>
        <li><strong>Managed</strong> → <code>em.remove</code> → Removed</li>
        <li><strong>Managed</strong> → <code>em.detach</code> / <code>em.close</code> → Detached</li>
        <li><strong>Detached</strong> → <code>em.merge</code> → Managed</li>
    </ul>

    <h2>Lazy vs Eager Loading</h2>
    <ul>
        <li>@OneToOne: Fetch type is <strong>EAGER</strong> by default.</li>
        <li>@OneToMany, @ManyToMany: Fetch type is <strong>LAZY</strong> by default.</li>
    </ul>

    <h2>JPA Relationships</h2>
    <h3>One-to-One Example</h3>
    <pre>
@Entity
public class Student {
    @OneToOne(fetch = FetchType.LAZY)
    private Passport passport;
}
    </pre>
    
    <h3>Many-to-Many Example</h3>
    <pre>
@Entity
public class Team {
    @ManyToMany(mappedBy = "teams", cascade = { CascadeType.MERGE, CascadeType.PERSIST })
    private List&lt;Match&gt; matches;
}
    
@Entity
public class Match {
    @ManyToMany(cascade = { CascadeType.MERGE, CascadeType.PERSIST })
    @JoinTable(
        name = "MATCH_TEAM",
        joinColumns = @JoinColumn(name = "MATCH_ID"),
        inverseJoinColumns = @JoinColumn(name = "TEAM_ID")
    )
    private List&lt;Team&gt; teams;
}
    </pre>

    <h2>Querying Data</h2>
    <h3>Using JPQL</h3>
    <pre>
TypedQuery&lt;Person&gt; query = entityManager.createQuery("SELECT p FROM Person p", Person.class);
List&lt;Person&gt; persons = query.getResultList();
    </pre>
    
    <h3>Using Named Query</h3>
    <pre>
@Entity
@NamedQuery(name = "find_all_persons", query = "SELECT p FROM Person p")
public class Person {
    ...
}
    </pre>

    <h3>Using Native Query</h3>
    <pre>
Query nativeQuery = entityManager.createNativeQuery("SELECT * FROM person", Person.class);
List&lt;Person&gt; persons = nativeQuery.getResultList();
    </pre>

    <h2>Spring Boot Integration</h2>
    <pre>
spring.jpa.show-sql=true
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.generate_statistics=true
logging.level.org.hibernate.type=trace
    </pre>
    
    <h2>Transaction Management with Spring</h2>
    <pre>
@Transactional
public class PersonJpaRepository {
    @PersistenceContext
    EntityManager entityManager;

    public Person findById(int id) {
        return entityManager.find(Person.class, id);
    }
}
    </pre>

    <h2>Entity Operations</h2>
    <pre>
// Insert or Update
Person newPerson = entityManager.merge(person);

// Delete
entityManager.remove(person);
    </pre>

    <h2>Automatic Timestamping</h2>
    <pre>
@CreationTimestamp
private LocalDateTime createdDate;

@UpdateTimestamp
private LocalDateTime lastUpdatedDate;
    </pre>
    
    <h2>Useful Links</h2>
    <ul>
        <li><a href="https://vladmihalcea.com/2016/10/20/the-best-way-to-implement-equals-hashcode-and-tostring-with-jpa-and-hibernate/">How to Implement Equals and HashCode in JPA</a></li>
        <li><a href="http://www.objectdb.com/java/jpa/persistence/delete#Orphan_Removal">JPA Orphan Removal</a></li>
    </ul>
</body>
</html>
