---
title: "Implementing Soft Delete in Spring Webflux with R2DBC"
date: "2024-11-12T12:00:22Z"
draft: false
tags: ["java", "programming", "spring", "r2dbc"]
categories: ["spring", "java", "programming", "r2dbc"]
cover:
  image: images/spring_soft_delete.png
  caption: "Implementing Soft Delete in Spring Webflux with R2DBC"
  hiddenInList: true
---

## Overview

Data management is a fundamental component in software development, especially when handling records that need removal from active use. Instead of permanently deleting records (a method known as “hard delete”), many applications use a technique called “soft delete.” The “soft delete” approach is a widely used solution that marks records as inactive without permanently removing them, enabling easy data recovery and historical tracking.

Currently, unlike Spring Data JPA and Hibernate, Spring Data R2DBC does not offer built-in annotations for automatically handling soft-delete. As a result, developers resort to the use of custom repository implementations or queries to achieve similar functionality.

In this article, we’ll examine soft delete, its benefits, and how to implement it in a Spring WebFlux application with R2DBC.

## What is Soft Delete

Soft delete is a data management method where records are flagged as inactive or “deleted” without being removed from the database. Typically, this involves adding a field to the entity, like deleted (a boolean) or deletedDate (a timestamp), to indicate that a record is no longer active. Instead of permanently deleting data, a soft delete marks the record as logically deleted, hiding it from standard queries while preserving it for potential recovery or auditing.

### Benefits of Soft Delete

1. Data Recovery: Soft delete allows for easy restoration of data. If a record is accidentally deleted, it can be quickly “undeleted” by resetting the flag, ensuring that no data is permanently lost.
2. Historical Data: Soft delete provides an audit trail. Organisations often need to keep historical data for compliance or reporting purposes, and soft delete enables this without crowding active data.
3. Data Integrity: In systems with complex relationships, permanently deleting a record can cause broken links and data inconsistencies. Soft delete addresses this by keeping related data intact while marking the deleted records as inactive.
4. Security and Compliance: Regulations often require data to be retained for specific periods. Soft delete enables developers to meet these compliance needs without making the data available to regular users.

## How to implement Soft Delete with Spring Reactive and R2DBC

If you’re interested in implementing this yourself, I’ve prepared a starter code — a simple blog application with basic CRUD endpoints and unit test cases. You can access the starter code from my GitHub repository using this [link](https://github.com/jelilio/spring-webflux-blog/tree/starter). So, let's get right to it.

### Step 1: Add a Field to Mark Records as Deleted

To implement this, add a field in your entity class to represent the deletion status. A more effective approach is to create an abstract class (**AbstractSoftDeletableEntity**), define the deletion status field within it, and have your entity class extend this abstract class. This field could either be a boolean (deleted) indicating whether the record is deleted or a timestamp (deletedDate) to specify when it was deleted. I recommend using a timestamp, as it provides the added detail of when the deletion took place:

{{< gist jelilio 73a3f198b7a483b8a65a8c38eb4d74bc >}}

### Step 2: Modify the entity class to extend the abstract class

Modifying the entity class to extend the **AbstractSoftDeletableEntity** creates a level of abstraction and separation of concern, thus adhering to the Single Responsibility Principle of object-oriented design.

{{< gist jelilio d8ce973eb03eb05507fbf7a22bc6342e >}}

### Step 3: Create a Generic Custom Repository Extending the SimpleR2dbcRepository

Many resources on implementing soft delete recommend using a custom repository for each entity, which can be cumbersome and hard to manage when an application has numerous entities. A better approach is to use a generic repository interface while providing custom implementations for the basic methods such as **counts**, **deleteById**, **deleteAll**, **findById**, and others.

{{< gist jelilio ef59fedae3d439db487c1cca867b2016 >}}

In the snippet above, I have the **SoftDeleteRepositoryImpl** implementing the **SoftDeleteRepository** interface, which provides another abstraction layer by listing abstract methods enhanced for soft delete operation.

{{< gist jelilio 13bd44b8a2e7565a016d3dd0fdde21a6 >}}

### Step 4: Modifying the Entity Repository to extend the Custom Repository Interface

Finally, modify the main repository interface by extending the **SoftDeleteRepository** and providing the entity class name and the id data type as the generic type arguments. It also provides a default implementation for the **findById**, **findAll**, **deleteById**, and **deleteAll** methods to make use of the soft delete custom implementations defined in the **SoftDeleteRepository** interface.

{{< gist jelilio 21d753af2593b1c6e8c348a0678b0647>}}

### Step 5: Implement Soft Delete in Service Layer

Once you’ve completed the steps above, you’re all set. No further implementation is needed at the domain service layer. Notably, both the controller and unit test cases also required no modifications.

To fetch all record (including deleted records) or deleted records, you can make use of query methods as shown in the snippet below:

{{< gist jelilio 25d4ed7eae21b048a4271a35ff0d42e4 >}}

## Conclusion

Soft delete is an effective and flexible method for managing data without permanently deleting it, making it ideal for applications needing data recovery, compliance, or historical data tracking. In this guide, we discussed what soft delete is, its benefits, and how it can be implemented in a Spring WebFlux application with R2DBC.

You can find the complete source code on [GitHub](https://github.com/jelilio/spring-webflux-blog)
