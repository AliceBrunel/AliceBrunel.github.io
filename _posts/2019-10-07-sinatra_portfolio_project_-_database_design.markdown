---
layout: post
title:      "Sinatra Portfolio project - Database design"
date:       2019-10-07 11:53:34 +0000
permalink:  sinatra_portfolio_project_-_database_design
---


## Problem and introduction
### The project
The project I will be working on is a small CMS to help small restaurants to manage their menus. The application will allow a restaurant team to submit menus ideas and select the one that is going to be published on the website. Each menu is composed of several meals. 
Therefore: 
* The users will be able to create meals and to add meals to a menu.
* The user whose role is "Manager" will be able to publish the menu on the website. 
* Each user will be able to delete their own menus but not the others'. 


### Tools 

The first step of this project is to make sure that the database schema is right. For that, I use the **[dbdiagram.io application](https://dbdiagram.io/)** , which allows you to draw your schema and helps to have a full vision of the database.

I would also recommend reading the excellent article from **[Kim Nguyen on Medium](https://medium.com/@kimtnguyen/relational-database-schema-design-overview-70e447ff66f9)**, which gives a quick but complete reminder of how to design your database. 

## Designing 
When I started to think about my project, I only thought that I would need Users, Menus, Meals and Recipes tables, and I couldn't see how they would be related together. So I started to draw a simple diagram, and played around with the relations. 

<div style="text-align:center">![DB schema 1](https://miro.medium.com/max/591/1*fqoNEx0ANehkelexuDQT5w.png)</div>

It became clear that I would need a join table at one point, but I could see how. What is interesting is that drawing your tables challenge the logic of your project. It seemed linguistically correct for me to think that a meal would have a specific recipe, and that I would need a recipe table. But in fact, in this case a recipe is an attribute of the meal, and not an item that should be linked to the meal. It became clear for me only when I started to work on the relations of all the tables.

<div style="text-align:center">![DB schema 2](https://miro.medium.com/max/550/1*RYhnv5O7psJ09qzxeEha2Q.png)</div>

So I got rid of the Recipe table and added the ingredient and directions field to the Meals table. 

Now how was I supposed to connect all these tables? I followed the process suggested by Kim Nguyen and asked myself:
* **Is there any one-to-one relationship** on my tables, so as to say is any of my table the extension of another. The answer is no. 
* **Is there any one-to-many relationship**, so as to say does any of my tables needs a foreign key to be linked to another table. Yes, because I need to make sure that my menus are linked to one user. Each menu belongs to one user. Each user can have many menus.
*  **Is there any many-to-many relationship**, so as to say is there any rows in any of my table that need to be connected to 1 or many rows in another table? 

Let's focus on the Menus and Meals table. What is my menu made of? It is made of several meals. Can each meal belong to several menus? Yes, some meals can be included in different menus. 

Let's build some menus: I will need a vegetarian menu, a pescetarian menu, a gluten-free menu. Obviously, I can add a strawberry pie to the vegetarian and pescetarian menu. So, a strawberry pie meal belongs to two menus in this case. I can also add an entry and a main to my menus. Therefore, they can be composed of 1 to many meals.

It's getting clearer, but let's schematise this:
<div style="text-align:center;">
<img src="https://miro.medium.com/max/1077/1*NTX7dm7j6TxNtuaxp1bUhA.png" alt="DB_schema_3" width="80%"/></div>


To build my menus, I just need a join table to associate a meal to a menu. Now, my tables relationships can be structured as below:
<div style="text-align:center">![DB schema 4](https://miro.medium.com/max/798/1*0we1VoXRNfMm6YkAKzQ3xw.png)</div>

I hope this can be of any help! I hope this could also help other students to get a good start on their project. It is not a complex architecture, but I think the step-by-step can be helpful to feel more confident about building databases.


