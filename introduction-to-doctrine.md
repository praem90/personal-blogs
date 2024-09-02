#Doctrine
 - Basics
 - Best Practices

# Doctrine Basics
 - Meta Mapping
 - Create
 - Read
 - Update
 - DQL
 - Associations

## Meta Mapping

```php
 <?php
 #[Entity, Table(name: 'users')]
 class User {
    #[Column, Id, GeneratedValue]
    public ?int $id = null;

    #[Column(length: 100)]
    public string $firstName = '';

    #[Column(length: 100)]
    public string $lastName = '';

    #[Column]
    public bool $isActive = true;
 }
```
It would be equivalent to the following table
```SQL
CREATE TABLE users (
    id INT NOT NULL AUTO_INCREMENT PRIMARY KEY,
    first_name string NOT NULL,
    last_name string NOT NULL,
    is_active TINYINT(1) NOT NULL,
)
```

## Create
```php
<?php
$user = new User();
$user->firstName = 'Mohan Raj';
$user->lastName = 'Murugesan';
$user->isActive = true;

$this->em->persist($user);
$this->em->flush();
```
This will run the following query
```SQL
INSERT INTO USERS (first_name, last_name, is_active)
values ('Mohan Raj', 'Murugesan', 1);
```

The AUTO_INCREMENT value of ID has been bind to the $user
object automatically


## Read
```php
<?php
$user = $this->em->find(User::class, 1);
```
This run the below query.
```SQL
SELECT t0.id as id_1,
    t0.first_name as first_name_2,
    t0.last_name as last_name_3,
    t0.is_active as is_active_4
FROM users as t0
WHERE t0.id = 1
```
A record is selected from the db and hydrated to the Entity
Note down the aliases

## Update

```php
 <?php
 $user = $this->em->find(User::class, 1);
 $user->isActive = false;

 $this->em->flush();
```

The Entity Manager tracks all the entities and
detects changes on flush by iterating over all the entities.

```SQL
 UPDATE users t0 set t0.is_active = 0 where t0.id = 1;
```

## DQL

Doctrine Query Language

```sql
SELECT p from App\Entity\User as p
where p.isActive = true
```

The DQL transpiled into SQL and hydrated into entities
The table name and column names has been detected automatically.
Note down the isActive, the database does not has a boolean type.
Doctrine automatically changes into 0 or 1

```SQL
SELECT t0.id as id_1,
    t0.first_name as first_name_2,
    t0.last_name as last_name_3,
    t0.is_active as is_active_4
FROM users as t0
WHERE t0.isActive = 1
```

## Associations

```php
<?php
 #[Entity, Table(name: 'users')]
class User {
    #[Column, Id, GeneratedValue]
    public ?int $id = null;

    #[Column(length: 100)]
    public string $firstName = '';

    #[Column(length: 100)]
    public string $lastName = '';

    #[Column]
    public bool $isActive = true;

    #[OneToMany(targetEntity: Role::class)]
    #[JoinTable(name: 'roles')]
    public Collection $roles;

    public function __construct() {
        $this->roles = new ArrayCollection();
    }
}
```

By default the associations are hydrated as Proxies and lazy Collections.
They are not loaded until we actually access them.

```php
<?php
$user = $this->em->find(User::class, 1);
foreach($user->roles as $role) {
    echo $role->name;
}
```

At the moment we try to iterate over the roles,
doctrine will query the database and return the role.

Note: This will lead to N+1 query
We can change it to EAGER to fetch all the roles
when we find the user or we can use joins

// #[OneToMany(targetEntity: Role::class, fetch: 'EAGER')]

## Batch Processing

As we know, doctrine's entity manager is tracks all the
changes to the database. It prepares the set of SQL queries
When we call the method `flush`.

### What would be the outcome the following code
```php
<?php
$handle = fopen('/tmp/users.csv', 'r');

while($row = fgetcsv($handle)) {
    $user = new User();
    $user->firstName = $row[0];
    $user->lastName = $row[1];
    $user->isActive = isset($row[2]) && $row[2] ? true : false;
    $this->em->persist($user);
}
$this->em->flush();
$this->em->clear();
```
How many queries and how many times will it be executed?

# Doctrine Best Practice

 - OLTP OnLine Transaction Processing
    Actually a transactional within ORM like db transaction
    For Reporting use SQL not doctrine
 - Entity should work without ORM and DB too
 - Design Entities first
 - Entities are not typed arrays rather it has behaviour
 - Keep Collections hidden in Entities
 - Entities are not db records
 - Should not validate an Entity
     Validate the request and once the Entity is craeted it must be valid state
     Once the entity constructed, it must be valid. Add static methods if required to build entity
 - Avoid Setters
 - Decouple from Application Layer use DTO. User::craeteFromRequest($request) // (bad)
 - Avoid Auto Generated IDs instead use UUIDs
 - Avoid Soft Deletes. Too old way of doing it
 - Try to make as immutable as it could be
 - Dont use EAGER loading
 - Avoid BI-Directional associations
 - Repositories are Services
 - Dont use Paginator. It is slower

## References
https://youtu.be/rzGeNYC3oz0?si=nC_TQzR2rTGyEUwr
https://youtu.be/qScVFyOGL_Y?si=mHtNUkdtBi8ChV1n



