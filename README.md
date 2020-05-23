# Mémos & bonnes pratiques en Java Persistence API

## Références

[Relations d'entités - Cours JPA](http://blog.paumard.org/cours/jpa/chap03-entite-relation.html)


## ManyToOne

```java
import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.ManyToOne;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@Entity
public class Employee {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;
    
    @Column(nullable = false)
    private String firstName;
    
    @Column(nullable = false)
    private String lastName;
    
    @Column(nullable = false)
    private String email;

    @ManyToOne
    @JsonIgnoreProperties("employees")
    private Service service;
 
    public Employee() {}
 
    public Employee(String firstName, String lastName, String email, Service service) {
         this.firstName = firstName;
         this.lastName = lastName;
         this.email = email;
         this.service = service;
    }

    // Getters Setters

}

```



```java
import java.util.List;

import javax.persistence.Column;
import javax.persistence.Entity;
import javax.persistence.FetchType;
import javax.persistence.GeneratedValue;
import javax.persistence.GenerationType;
import javax.persistence.Id;
import javax.persistence.OneToMany;

import com.fasterxml.jackson.annotation.JsonIgnoreProperties;

@Entity
public class Service {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private Long id;

    @Column(name = "name", nullable = false)
    private String name;
    
    @OneToMany(mappedBy="service", fetch = FetchType.EAGER)
    @JsonIgnoreProperties("service")
    private List<Employee> employees;


    public Service() {}

    public Service(String name) {
        this.name = name;
    }
    
    // Getters Setters
    
 }

```
Exemple d'utilisation :

```java
@SpringBootApplication
public class SprintBootRestApplication {

	private static final Logger log = LoggerFactory.getLogger(SprintBootRestApplication.class);

	...

	@Bean
  public CommandLineRunner demo(EmployeeRepository employeeRepository, ServiceRepository serviceRepository) {
    return (args) -> {

	Service service1 = serviceRepository.save(new Service("Marketing service"));
	
	Employee employee1 = new Employee("Jack", "Bauer", "jack.bauer@mail.com", service1);
	employeeRepository.save(employee1);
	
	Employee employee2 = new Employee("Bernard", "Cornwell", "jack.bauer@mail.com", service1);
	employeeRepository.save(employee2);

	Service service2 = serviceRepository.save(new Service("Human Resources service"));
	
	Employee employee3 = new Employee("John", "Snow", "john.snow@mail.com", service2);
	employeeRepository.save(employee3);

	Employee employee4 = new Employee("Chandler", "Bing", "chandler.bing@mail.com", service2);
	employeeRepository.save(employee4);

    ...
    }
  }

```
