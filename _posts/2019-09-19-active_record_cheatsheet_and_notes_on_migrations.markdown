---
layout: post
title:      "Active Record: cheatsheet and notes on migrations"
date:       2019-09-19 12:13:56 +0000
permalink:  active_record_cheatsheet_and_notes_on_migrations
---


## What is Active Record and how does it work? 
**Active Record is a Ruby library that provides an Object relational Mapping (ORM).** Columns are accessed by methods, and integrates SQL queries in its available pre-made methods. It also provides methods for the four basic functions  create, read, update and delete (CRUD). The main object of Active Record is to "map" the table columns to the corresponding object attributes of your database. It is called a **design pattern**.

**About Rake**
Rake can be used in Ruby projects as a Gem. It provides tasks that can useful to manage your database migration.

**About migrations**
A migration, by definition, is a change to the database schema. Migrations allow to version the changes made to your database, to make sure you can revert them, and manage the evolution of your project.

## Structuring your migrations
### To do a migration you need to create:
* A *db* folder where you will put your migration folder
    - A *migrate or migration* folder which will contain the migration files
    - The migration files starting with a date stamp or an order and the name of the migration
* An *app* folder where you will put your models folder
    - A *models* folders which will contain the model files
    - The models files

### What is a migration file? 
Each migration files represents a migration, so each step of the evolution of your project. When a migration is ran, the leading digits of your migration files are used to order the migration: 001_create_table.rb will be migrated before 002_add_column.rb. This is how Active Record maintain a version control.

A migration file in Active Record uses Class Inheritance and should be written as below:
```
class CreateStudentsTable < ActiveRecord::Migration[5.2]
    # Some Active Record methods corresponding to the migration title
    create_table :students do |i|
        i.string :name
    end
end
```

### What is a model? 
Active Record provide gateways to help object classes to talk with their corresponding database. It is done by Class Inheritance. By making your object class a subclass of Active Record, you create a model that benefits from Active Record's built-in methods. For instance:
```
class Student < ActiveRecord::Base
end
```
The Student model is automatically linked to the ```student``` table.
### How to create these files and folders? 

It is possible to manually create all of these files and folder, or to use the Active Record Rake command ```rake db:create_migration NAME=create_table```.

## Operating a migration
The migration process differs from a project to another. These are guidelines to make sure the migration steps are safe for your program. 

### First migration: create a table
```
class CreateTableName < ActiveRecord::Migration[5.2]
    def change
        create_table :table_items do |i|
				    i.string :name
		end
    end
end
```
List in the method all the columns of your table with their datatype, except the ID, which is automatically created. Always use the format [parameter.datatype :symbol].
### Second migration: add columns
```
class CreateTableName < ActiveRecord::Migration[5.2]
    def change
		    add_column :table_items, :surname, :string
    end
end
```

It is possible to add multiple columns. The symbole order means that in the the table_items table, you add a column surname with a string datatype.

### Using Rake command 
* To migrate all the migration files, use the rake command ```rake db:migrate``` 
* To revert a migration use  ```rake db:rollback```
* To view your project Rake commands use ```rake -T```

