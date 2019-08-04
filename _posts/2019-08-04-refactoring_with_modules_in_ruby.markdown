---
layout: post
title:      "Refactoring with modules in Ruby"
date:       2019-08-04 13:17:33 +0000
permalink:  refactoring_with_modules_in_ruby
---

Each new step in the learning of Ruby seems more interesting than the other but also more confusing. To keep in mind the structure of an optimised object oriented model, I tried to keep notes on these confusing concepts. In the Intro to Modules Lab, I noticed I made mistakes essentially related to a lack of organisation while writing my code. I kept forgetting about adding the required relative path, about adding the extend or include keyword to refer to the module...

Thus, I decided to take a step-by-step approach that I am sharing here.

### The structure of your program

#### A - The environment file in config folder
* This is were we will put all the require_relative path to our modules and classes
* This environment file will have to be updated each time a new module file is created

#### B - The lib folder 
* Contains the program classes
* Contains the concerns folder where modules are stored

#### C - The modules' Concerns files in lib folder

### The refactoring check-list

##### Step 1 
Read the classes files and decide which methods you want to put in external modules :
* If you find the same method in your Class files, you may consider refactoring the repeted method in a module file
* Comment the methods that you want to refactor
* Are these Class methods? If yes, you will have to use the **extend** keyword and get rid of the *self* reference when defining the method
* Are these Instance methods? If yes, you will have to use the **include** keyword.

##### Step 2
* Create a module file in Concerns (C)
* Define your methods in the module file 
* Put the corresponding require_relative path in your environment file

##### Step 3
* Go back to the Class files and put the **extend or include keyword**
* Run your test if you are in a test driven configuration 
* Delete the commented methods

I tend to get distracted by a few things when I code (like, "why is this code written like that?", "did I really make this mistake?", "I should change the order of these methods", "Oh, I forgot an end...") so having a simple check-list can actually save me some times. I hope it can help someone else too!
