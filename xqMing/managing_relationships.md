---
layout: default
title: Managing relationships
permalink: /managing-relationships/
redirect_from:
  - /managing_relationships.html
sitemap:
    priority: 0.7
    lastmod: 2016-03-26T18:40:00-00:00
---

# <i class="fa fa-sitemap"></i> 管理实体间映射关系  Managing relationships

如果你在使用JPA，则可以通过 [entity sub-generator]({{ site.url }}/creating-an-entity/) 创建实体之间映射关系。
 

## 介绍 
注意只有当你使用了 JPA 才可以生成映射关系。如果你选择了 [Cassandra]({{ site.url }}/using-cassandra/) 或者 [MongoDB]({{ site.url }}/using-mongodb/)，则不会起作用。

映射关系只在两个实体间起作用，JHipster 将生成如下的代码：

- 使用 JPA 管理生成实体的映射关系的代码
- 创建 Liquibase 的修改日志，以便在数据库中体现出它们映射关系
- 生成 AngularJS 前端代码，以便在用户界面中以图形方式管理这种映射关系


## JHipster UML 和 JDL Studio
这一章节描述如何使用标准的命令行界面创建实体映射关系。不过如果你想创建大量的实体和映射关系，你可能会喜欢通过图形工具来实现。

针对这种情况，有两个可供选择的工具：

- [JHipster UML]({{ site.url }}/jhipster-uml/)，可以让你使用 UML 编辑器。

- [JDL Studio]({{ site.url }}/jdl-studio/)，我们的在线工具，通过使用我们的特定领域语言 [JDL](https://jhipster.github.io/jdl/) 来创建实体间映射关系。


你可以使用 `import-jdl` 子生成器，通过读取 JDL 文件，来生成实体和映射关系，命令为 `yo jhipster:import-jdl your-jdl-file.jh`。


## 可能的映射关系种类

因为我们使用来 JPA ，所以普通的一对多、多对一，多对多和一对一的映射关系是可以生成的：
As we use JPA, the usual one-to-many, many-to-one, many-to-many and one-to-one relationships are available:

1. [双向一对多的映射关系](#1)
2. [单向多对一的映射关系](#2)
3. [单向一对多的映射关系](#3)
4. [在相同的两个实体对多映射关系二一](# 4)
5. [一个多对多的映射关系](#5)
6. [一对一的映射关系](#6)
7. [单向的一对一映射关系](#7)

1. [A bidirectional one-to-many relationship](#1)
2. [A unidirectional many-to-one relationship](#2)
3. [A unidirectional one-to-many relationship](#3)
4. [Two one-to-many relationships on the same two entities](#4)
5. [A many-to-many relationship](#5)
6. [A one-to-one relationship](#6)
7. [A unidirectional one-to-one relationship](#7)

_Tip: the `User` entity_

注意在 `User` 这个实体中，JHipster 是经过特殊处理的。可能存在多对一的映射关系，比如在 `Car` 和 `User` 间就存在多对一的映射关系。这样就会生成特殊的查询映射关系，
Please note that the `User` entity, which is handled by JHipster, is specific. You can do `many-to-one` relationships to this entity (a `Car` can have a many-to-one relationship to a `User`). This will generate a specific query in your new entity repository, so you can filter your entity on the current security user, which is a common requirement. On the generated AngularJS client UI you will have a dropdown in `Car` to select a `User`.

## <a name="1"></a> 双向一对多映射关系 A bidirectional one-to-many relationship
举个例子，`Owner` 和 `Car` 这个两个实体。一个车主可以有多辆车，而一辆车只有一个车主。
 
所以这是一个简单的一对多映射关系
So this is a simple one-to-many relationship (one owner has many cars) on one side, and a many-to-one relationship (many cars have one owner) on the other side:

    Owner (1) <-----> (*) Car

我们需要先创建 `Owner` 实体。下面仅仅列出 JHipster 将询问的映射关系的相关问题：

    yo jhipster:entity Owner
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? car
    ? What is the type of the relationship? one-to-many
    ? What is the name of this relationship in the other entity? owner

注意这里我们选择默
Please note that we selected the default options concerning the names of the relationships.

现在我们生成实体 `Car`，同上仅列出与生成映射关系相关的问题：

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Owner
    ? What is the name of the relationship? owner
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Owner' do you want to use? id

你同样可以通过使用下面的 JDL 来实现：

    entity Owner
    entity Car

    relationship OneToMany {
      Owner{car} to Car{owner}
    }

这样你就完成了在两个实体间生成一对多的映射关系！在新生成的 AngularJS 前端代码所对应的UI界面，会有一个 `Car` 的下拉选项让你选择 `Owner`。
That's it, you now have a one-to-many relationship between those two entities! On the generated AngularJS client UI you will have a dropdown in `Car` to select a `Owner`.

## <a name="2"></a> 单向多对一关系  A unidirectional many-to-one relationship

在前面的例子描述了一个双向的关系，通过一个 `Car` 的实例，你可以找到 `Owner`，通过一个 `Owner` 的实例，你可以得到他所有的 `Car`。

而一个单向的多对一关系，意味着汽车知道他们的车主，但是反过来就不行。

A many-to-one unidirectional relationship means that the cars know their owner, but not the opposite.

    Owner (1) <----- (*) Car

你生成这样的映射关系，会有两个原因：
You would do that relationship for two reasons:

- 从业务的角度来看，你要用这种方式来使用你的实体类，是因为你并不想去生成一个没有意义的API。

- 从性能的角度来考虑，你这样使用 `Owner`，可以为系统带来性能上的提升。（因为它不必管理汽车的集合）。

在这个案例中，你同样需要先创建 `Owner` 实体，生成的关系如下：

    yo jhipster:entity Owner
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? No

And then the `Car` entity, as in the previous example:

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Owner
    ? What is the name of the relationship? owner
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Owner' do you want to use? id

This will work as in the previous example, but you won't be able to add or remove cars from the `Owner` entity. On the generated AngularJS client UI you will have a dropdown in `Car` to select a `Owner`.
This is the corresponding JDL:

    entity Owner
    entity Car

    relationship ManyToOne {
      Car{owner} to Owner
    }


## <a name="3"></a> A unidirectional one-to-many relationship

A one-to-many unidirectional relationship means that the `Owner` instance can get its collection of cars, but not the opposite. It is the opposite from the previous example.

    Owner (1) -----> (*) Car

This type of relationship is not provided by default in JHipster at the moment, see [#1569](https://github.com/jhipster/generator-jhipster/issues/1569) for more information.

You have two solutions for this:

- Do a bidirectional mapping, and use it without modification: this is our recommended approach, as it is much simpler
- Do a bidirectional mapping, and then modify it to transform it into a unidirectional mapping:
    - Remove the "mappedBy" attribute on your `@OneToMany` annotation
    - Generate the required join table: you can do a `mvn liquibase:diff` to generate that table, see the [documentation about using Liquibase diff]({{ site.url }}/development/)

This is not supported with JDL as it isn't in JHipster.

## <a name="4"></a> Two one-to-many relationships on the same two entities

For this example, a `Person` can be the owner of many cars, and he can also be the driver of many cars:

    Person (1) <---owns-----> (*) Car
    Person (1) <---drives---> (*) Car

For this we need to use the relationship names, which we have left with their default values in the previous examples.

Generate the `Person` entity, which has tow one-to-many relationships to the `Car` entity:

    yo jhipster:entity Person
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? ownedCar
    ? What is the type of the relationship? one-to-many
    ? What is the name of this relationship in the other entity? owner
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? drivedCar
    ? What is the type of the relationship? one-to-many
    ? What is the name of this relationship in the other entity? driver

Generate the `Car` entity, which use the same relationship name has was configured in the `Person` entity:

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Person
    ? What is the name of the relationship? owner
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Person' do you want to use? id
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Person
    ? What is the name of the relationship? driver
    ? What is the type of the relationship? many-to-one
    ? When you display this relationship with AngularJS, which field from 'Person' do you want to use? id

The same can be achieved using the below JDL as well

    entity Person
    entity Car

    relationship OneToMany {
      Person{ownedCar} to Car{owner}
    }

    relationship OneToMany {
      Person{drivedCar} to Car{driver}
    }

A `Car` can now have a driver and a owner, which are both `Person` entities. On the generated AngularJS client UI you will dropdowns in `Car` to select a `Person` for `owner` field and `driver` field.

## <a name="5"></a> A many-to-many relationship

A `Driver` can drive many cars, but a `Car` can also have many drivers.

    Driver (*) <-----> (*) Car

At the database level, this means we will have a join table between the `Driver` and the `Car` tables.

For JPA, one of those two entities will need to manage the relationship: in our case, that would be the `Car` entity, which will be responsible to add or remove drivers.

Let us generate the non-owning side of the relationship, the `Driver`, with a many-to-many relationship:

    yo jhipster:entity Driver
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? car
    ? What is the type of the relationship? many-to-many
    ? Is this entity the owner of the relationship? No
    ? What is the name of this relationship in the other entity? driver

Then generate the `Car`, with the owning side of the many-to-many relationship:

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Driver
    ? What is the name of the relationship? driver
    ? What is the type of the relationship? many-to-many
    ? Is this entity the owner of the relationship? Yes
    ? When you display this relationship with AngularJS, which field from 'Driver' do you want to use? id

The same can be achieved using the below JDL as well

    entity Driver
    entity Car

    relationship ManyToMany {
      Car{driver} to Driver{car}
    }

That's it, you now have a many-to-many relationship between those two entities! On the generated AngularJS client UI you will have a multi-select dropdown in `Car` to select multiple `Driver` since `Car` is the owning side.

## <a name="6"></a> A one-to-one relationship

Following our example, a one-to-one relationship would mean that a `Driver` can drive only one `Car`, and a `Car` can only have one `Driver`.

    Driver (1) <-----> (1) Car

Let us create the non-owning side of the relationship, in our case the `Driver`:

    yo jhipster:entity Driver
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Car
    ? What is the name of the relationship? car
    ? What is the type of the relationship? one-to-one
    ? Is this entity the owner of the relationship? No
    ? What is the name of this relationship in the other entity? driver

Then generate the `Car`, which owns the relationship:

    yo jhipster:entity Car
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Driver
    ? What is the name of the relationship? driver
    ? What is the type of the relationship? one-to-one
    ? Is this entity the owner of the relationship? Yes
    ? What is the name of this relationship in the other entity? car
    ? When you display this relationship with AngularJS, which field from 'Driver' do you want to use? id

The same can be achieved using the below JDL as well

    entity Driver
    entity Car

    relationship OneToOne {
      Car{driver} to Driver{car}
    }

That's it, you now have a one-to-one relationship between those two entities! On the generated AngularJS client UI you will have a dropdown in `Car` to select a `Driver` since `Car` is the owning side.

## <a name="7"></a> A unidirectional one-to-one relationship

A unidirectional one-to-one relationship means that the `citizen` instance can get its passport, but the `passport` instance can't get to its owner.

    Citizen (1) -----> (1) Passport

Generate the `Passport` entity first, without any relationship to its owner:

    yo jhipster:entity Passport
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? No

Then, generate the `Citizen` entity:

    yo jhipster:entity Citizen
    ...
    Generating relationships with other entities
    ? Do you want to add a relationship to another entity? Yes
    ? What is the name of the other entity? Passport
    ? What is the name of the relationship? passport
    ? What is the type of the relationship? one-to-one
    ? Is this entity the owner of the relationship? Yes
    ? What is the name of this relationship in the other entity? citizen
    ? When you display this relationship with AngularJS, which field from 'Passport' do you want to use? id

After doing this, a `Citizen` possesses a passport, but no `Citizen` instance is defined in `Passport`. On the generated AngularJS client UI you will have a dropdown in `Citizen` to select a `Passport` since `Citizen` is the owning side.
This is the corresponding JDL:


    entity Citizen
    entity Passport

    relationship OneToOne {
      Citizen{passport} to Passport
    }
