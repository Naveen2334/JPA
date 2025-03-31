<!DOCTYPE html>
<html>
<head>
    <meta charset="UTF-8">
    <title>JPA & Hibernate Cheat Sheet</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            line-height: 1.6;
        }
        h1, h2, h3 {
            color: #333;
        }
        code {
            background: #f4f4f4;
            padding: 2px 5px;
            border-radius: 5px;
        }
        pre {
            background: #f4f4f4;
            padding: 10px;
            border-radius: 5px;
            overflow-x: auto;
        }
    </style>
</head>
<body>
    <h1>JPA & Hibernate Cheat Sheet</h1>
    
    <h2>Transaction Management with EntityManager</h2>
    <pre><code>entityManager.getTransaction().begin();
entityManager.persist(&lt;some-entity&gt;);
entityManager.getTransaction().commit();
entityManager.clear();

SomeEntity entity = entityManager.find(SomeEntity.class, 1);</code></pre>
    
    <h2>Lazy and Eager Loading</h2>
    <ul>
        <li><code>@OneToOne</code> fetch type is <strong>EAGER</strong> by default.</li>
        <li>Lists & Sets fetch type is <strong>LAZY</strong> by default.</li>
    </ul>
    
    <h2>Entity Lifecycle</h2>
    <pre><code>New ---> em.persist ---> Managed
New ---> em.merge ---> Managed

Managed ---> em.remove ---> Removed

Managed ---> em.find ---> Managed
Managed ---> query.getResultList ---> Managed
Managed ---> query.getSingleResult ---> Managed

Managed ---> em.detach ---> Detached
Managed ---> em.close ---> Detached
Managed ---> em.clear ---> Detached

Detached ---> em.merge ---> Managed</code></pre>
    
    <h2>JPA Annotations</h2>
    <pre><code>@Entity
@Table(name = "ADDRESS")
public class Address {
    @Id
    @SequenceGenerator(
        name = "address_seq",
        sequenceName = "address_seq",
        allocationSize = 1
    )
    @GeneratedValue(
        strategy = GenerationType.SEQUENCE,
        generator = "address_seq"
    )
    private long id;
}</code></pre>
    
    <h2>Deleting Dependent Children</h2>
    <pre><code>@OneToMany(mappedBy="foo", orphanRemoval=true)
private List&lt;Bikes&gt; bikes;</code></pre>
    
    <h2>Many-to-Many Relationships</h2>
    <pre><code>@Entity
public class Team {
    @ManyToMany(cascade = { CascadeType.MERGE, CascadeType.PERSIST }, mappedBy="teams")
    private List&lt;Match&gt; matches;
}

@Entity
public class Match {
    @ManyToMany(cascade = { CascadeType.MERGE, CascadeType.PERSIST })
    @JoinTable(
        name="MATCH_TEAM",
        joinColumns={@JoinColumn(name="MATCH_ID", referencedColumnName="ID")},
        inverseJoinColumns={@JoinColumn(name="TEAM_ID", referencedColumnName="ID")}
    )
    private List&lt;Team&gt; teams;
}</code></pre>
    
    <h2>Spring Boot Configuration for JPA</h2>
    <pre><code>spring.h2.console.enabled=true
spring.jpa.show-sql=true
logging.level.org.hibernate.type=trace
spring.jpa.properties.hibernate.format_sql=true
spring.jpa.properties.hibernate.generate_statistics=true
logging.level.org.hibernate.stat=debug</code></pre>
    
    <h2>JPA Queries</h2>
    <h3>Named Query</h3>
    <pre><code>@Entity
@NamedQuery(name="find_all_persons", query="select p from Person p")
public class Person { ... }</code></pre>
    
    <h3>Native Query</h3>
    <pre><code>Query nativeQuery = em.createNativeQuery("SELECT * FROM person", Person.class);
List&lt;Person&gt; persons = nativeQuery.getResultList();</code></pre>
    
    <h3>Using JdbcTemplate</h3>
    <pre><code>List&lt;Person&gt; persons = jdbcTemplate.query(
    "select * from person", new BeanPropertyRowMapper&lt;Person&gt;(Person.class));</code></pre>
    
    <h2>Transactions & Persistence Context</h2>
    <pre><code>@Transactional
public void foo() {
    Person person = new Person("Hansen");
    em.persist(person);
    person.setName("Meier");
}</code></pre>
    
    <h2>Column Properties</h2>
    <pre><code>@Column(
    name="fullname",
    nullable=false,
    unique=true,
    insertable=false,
    updateable=false,
    length=20
)
private String name;</code></pre>
    
    <h2>Automatically Insert Timestamp</h2>
    <pre><code>@CreationTimestamp
private LocalDateTime createdDate;

@UpdateTimestamp
private LocalDateTime lastUpdatedDate;</code></pre>
    
    <h2>Lazy vs Eager Loading in One-to-One</h2>
    <pre><code>@Entity
public class Student {
    @OneToOne(fetch=FetchType.LAZY)
    private Passport passport;
}</code></pre>
    
    <h2>Bidirectional One-to-One Relationship</h2>
    <pre><code>@Entity
public class Passport {
    @OneToOne(fetch=FetchType.LAZY, mappedBy="passport")
    private Student student;
}</code></pre>
    
    <h2>EntityManager Operations</h2>
    <pre><code>Person person = entityManager.find(Person.class, id);
entityManager.persist(person);
entityManager.merge(person);
entityManager.remove(person);
entityManager.clear();</code></pre>
    
    <h2>JPA Delete Strategies</h2>
    <pre><code>@OneToMany(mappedBy="foo", orphanRemoval=true)
private List&lt;Bikes&gt; bikes;</code></pre>
</body>
</html>
