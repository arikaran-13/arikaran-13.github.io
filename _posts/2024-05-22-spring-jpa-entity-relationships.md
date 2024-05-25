---
title: Spring Data JPA Relationships Entity Relationships
date: 2024-05-22 10.10PM
categories: [spring boot]
tag: [java , spring boot]
image:
  path: /assets/images/spring_jpa_relationship/jpa_spring.jpeg
---

## Introduction

Spring Data JPA simplifies the implementation of data access layers in Java applications. One of its powerful features is the ability to manage relationships between entities easily. Relationships in databases allow you to define how different tables are connected, which is crucial for structuring data in a meaningful way. With Spring Data JPA, you can map these relationships directly in your Java classes using annotations. This not only makes your code cleaner but also ensures that your database operations are efficient and consistent. In this guide, we'll explore the different types of relationships you can define and how to implement them using Spring Data JPA.

## One-To-One

In a one-to-one relationship, each item or row in one table matches exactly one item in another table. 

For example, a customer can have only one home address, and that home address is linked to only one customer. 

When we create a one-to-one relationship in the customer table, we have a separate column (called a foreign key column) that contains the address ID of that particular customer.

There are three ways to implement a one-to-one relationship:

1. Unidirectional One-To-One Relationship
2. Bidirectional One-To-One Relationship
3. Using a Separate Join Table

Let's see the customer and address example in code to understand the types of one-to-one relationships:

### Unidirectional One-To-One Relationship

#### 1. Create a Spring Project
using [start.spring.io](https://start.spring.io) with the following dependencies:

```xml
<dependencies>
  <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>
  <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-web</artifactId>
  </dependency>
  <dependency>
     <groupId>com.mysql</groupId>
     <artifactId>mysql-connector-j</artifactId>
     <scope>runtime</scope>
  </dependency>
  <dependency>
     <groupId>org.projectlombok</groupId>
     <artifactId>lombok</artifactId>
     <optional>true</optional>
  </dependency>
  <dependency>
     <groupId>org.springframework.boot</groupId>
     <artifactId>spring-boot-starter-test</artifactId>
     <scope>test</scope>
  </dependency>
</dependencies>

```
#### 2. Connect with DB

```yml
spring:
  datasource:
    url: jdbc:mysql://localhost:3306/demo
    username: root
    password: root
  jpa:
    hibernate:
      ddl-auto: update
    properties:
      hibernate:
        dialect: org.hibernate.dialect.MySQLDialect

```
#### 3. Create Two Entities

**Customer.java**

```java
package com.example.entity;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
public class Customer {

    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String name;
    @OneToOne(cascade = CascadeType.ALL)
    @JoinColumn(name = "customer_address")
    private Address address;
}

```

Here, `@Entity` informs Spring Data JPA to treat this as a table. We are using `@Getter` and `@Setter` to reduce boilerplate code, and `@Id` to indicate that this field is the primary key. The `@OneToOne` annotation specifies that each row of the Customer entity will have a reference to each row of the Address entity.

**Address.java**

```java
package com.example.entity;

import jakarta.persistence.*;
import lombok.Getter;
import lombok.Setter;

@Entity
@Getter
@Setter
public class Address {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String city;
    private String country;
    private Customer customer;
}

```
<hr>
**Note**:
In one-to-one relationships, either entity can be the owning or the reference entity.
In one-to-many relationships, the "many" side is the owning side, and the "one" side is the reference side.
<hr>

### Bidirectional One-To-One Relationship

In a Bidirectional One-to-One relationships both the entities or table will associate to each other.This is achieved by annotating `@OneToOne` on both entities, designating one as the owning side and the other as the reference side.

Example: Implementing Bidirectional One-to-One Relationship

Let's modify the `Customer` and `Address` entities to establish a bidirectional relationship.

`Customer.java`

```java

    @OneToOne(mappedBy = "customer")
    private Address address;
```
`Address.java`

```java
    @OneToOne
    private Customer customer;
```

Here,

- The `@OneToOne(mappedBy = "customer")` annotation in the `Customer` entity specifies that the `address` field is mapped by the `customer` field in the `Address` entity.

- The `@OneToOne` annotation in the `Address` entity indicates that the `customer` field is the owning side of the relationship.

**Foreign Key Creation:**

By using the `mappedBy` attribute in the `Customer` entity, we inform Spring JPA that the foreign key column should be created in the `Address` table. As a result, Spring JPA will create a new column in the `Address` table to maintain this relationship.

**Created table with forign key column in address table**:

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/one-to-one-bi.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

### Using a Separate Join Table

To maintain a one-to-one relationship between `Customer` and `Address` using a separate join table, you can use the `@JoinTable` annotation. This approach will create a separate table to maintain the one-to-one relationship .

Example: Modifying `Customer.java`

First, modify the existing `Customer.java` file to include the `@JoinTable` annotation:

```java

@Entity
@Getter
@Setter
@Table(name = "customers")
public class Customer {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;
    @OneToOne
    @JoinTable(name = "customer_address",joinColumns = @JoinColumn(name = "customer_id"),
    inverseJoinColumns = @JoinColumn(name = "address_id"))
    private Address address;

}

```
`@JoinTable` → Spring JPA will create a separate joining table to maintain one to one relationship with custom table name  name as `customer_address`.

`joinColumns` → will name the owning side column name for customer table as `customer_id`

`inverseJoinColumns` → the reference side column name for item table as `address_id`

**Created join table `customer_address`**

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/customer_address_join_table_one-to-one.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

## One-To-Many

In a one-to-many relationship, one entity is associated with multiple entities. For example, a Department can have multiple Employees. This relationship can be implemented in two ways:

1. Unidirectional One-To-Many
2. Bidirectional One-To-Many

### Unidirectional One-To-Many
Let's explore these with examples using two entities: Department and Employee.

Department.java

```java
package com.example;

import jakarta.persistence.*;
import java.util.List;

@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String name;
    @OneToMany
    private List<Employee> employeeList;
}

```

Employee.java

```java
package com.example;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String name;
}

```

In the provided code snippet, we define two entities: "department" and "employee," each comprising various fields. With Spring JPA, these entities translate into corresponding database tables.

When we mark a class as @Entity it tells spring Data Jpa to create a database tables

So when our application runs spring Jpa takes these entity classes and turns into tables:

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/one_to_many.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

We use `@Id` to tell Spring Jpa that the field annotated with id will treat as primary key of the table.

For example, in our `Department` class, we have an `id` field marked with `@Id`, indicating that each department in the database will be uniquely identified by its `id`.

In our department class we are having an one-to-many relationship by using `@OneToMany` annotation , it indicate one department can have multple employees so the Spring Jpa will create new table for maintaining OneToMany relation called: `department_employeeList`

here the table name automatically created by spring data jpa by name of first table + employeeList field name.

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/join_table.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

<hr>
**Note**: "In a one-to-many relationship, it's important to note that the 'many' side is treated as the owning side. This means that the table representing the 'many' side will contain the foreign key column to establish the relationship.”
<hr>

Instead of creating a separate table for one-to-many relation we can use @JoinColumn annotation to add a new column in our owning side entity or child table (Employee table)

```java
@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;

    private String name;

    @OneToMany
    @JoinColumn(name = "department_id")
    private List<Employee> employeeList;
}
```

here `@JoinColumn` annotation is used in the Department class to specify that the `department_id` column in the Employee table should be used to establish the relationship between departments and employees we are telling spring jpa to add a column in its owning side table (Employee) named as `department_id`.

employee table created by jpa: 

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/employee_table_created_by_jpa.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

### BiDirectional One-To-Many relationship:

In a bidirectional one-to-many relationship, both the owning side entity (eg Employee) and the referenced entity (Department) are annotated with `@OneToMany` and `@ManyToOne`. By using the `mappedBy` attribute, we inform JPA to create a foreign key column in the specified table. This establishes the connection between the two entities, allowing for navigation in both directions.

Department.java

```java
package com.example;

import jakarta.persistence.*;

import java.util.List;

@Entity
public class Department {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;

    private String name;

    @OneToMany(mappedBy = "department")   
    private List<Employee> employeeList;
}

```

here mappedBy = “department” means

we're indicating to Spring JPA that the foreign key column should be created based on the specified field (`department` in the `Employee` class) in the owning side of the relationship. This ensures that the relationship between the two entities is properly established, allowing for efficient navigation between them.

Employee.java

```java
package com.example;

import jakarta.persistence.*;

@Entity
public class Employee {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String name;
    @ManyToOne
    @JoinColumn(name = "department_id")
    private Department department;
}

```  

`@JoinColumn(name = "department_id")`: This annotation instructs JPA to create a column named `department_id` in the `Employee` table to establish the association with the `Department` table.

`@ManyToOne`: This annotation indicates that many employees can be associated with one department, defining the relationship between the `Employee` and `Department` entities.

department table:

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/department_table_bi_directional.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

employee table:

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/bidirectional_employee_table.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

## Many-to-Many

<hr>
**Note** :

1. In Many-to-Many any entities can be owning side or reference side.
2. In Many-To-Many we need to use Set<Object> not List<Object>.
<hr>

So in many to many relationships one entity or table can have multiple association or reference to other table or entity

**Example** : A customer can purchase multiple item and item can belongs to multiple customer

### UniDirectional Many-To-Many relationship:

Lets first see how to create unidirectional many to many relationship between customer and item:

Item.java:

```java
package com.example;

import jakarta.persistence.Entity;
import jakarta.persistence.GeneratedValue;
import jakarta.persistence.GenerationType;
import jakarta.persistence.Id;
import lombok.Getter;
import lombok.Setter;

import java.util.Set;

@Entity
@Getter
@Setter
public class Item {
    @Id
    @GeneratedValue(strategy = GenerationType.AUTO)
    private int id;
    private String name;
    private Set<Customer> customers;
}

```

Customer.java:

```java
@Entity
@Getter
@Setter
@Table(name = "customers")
public class Customer implements Serializable {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private int id;
    private String name;
    @ManyToMany
    private Set<Item> itemSet;

}
```

here we can see that we are simply using @ManyToMany annotation where spring jpa will create a table with name `customers_item_set` ( owning side table name + owning side instance field name)

<div style="text-align: center;">
  <img src="/assets/images/spring_jpa_relationship/many-to-many-uni-dir.png" alt="tables_created" style="width: 50%; height: auto;"/>
</div>

if we want a custom name for the  joining table then we can use @JoinTable annotation :

```java
    @ManyToMany
    @JoinTable(name = "customers_items",joinColumns = @JoinColumn(name = "customer_id"),
    inverseJoinColumns = @JoinColumn(name = "item_id"))
    private Set<Item> itemSet;
```

`@JoinTable` → spring jpa will create joining table for many to many relationship with custom table name .

`joinColumns` → will name the owning side column name for customer table as customer_id

`inverseJoinColumns` → the reference side column name for item table as item_id.

### BiDirectional Many-To-Many relationship:

In case of bidirectional relationship we will add @ManyToMany annotation on both entites and we use mappedBy property on any one entity to tell spring jpa which entity or table should consider as owning side and creates joining table .

Customer.java

```java
    @ManyToMany
    @JoinTable(name = "customers_items",joinColumns = @JoinColumn(name = "customer_id"),
    inverseJoinColumns = @JoinColumn(name = "item_id"))
    private Set<Item> itemSet;

```

Item.java

```java
    @ManyToMany(mappedBy = "itemSet")
    private Set<Customer> customers;
```

"Thank you for taking the time to explore entity relationships with Spring Data JPA. I hope this guide has been helpful in understanding how to implement and manage relationships in your Java applications. **Happy coding!**"
