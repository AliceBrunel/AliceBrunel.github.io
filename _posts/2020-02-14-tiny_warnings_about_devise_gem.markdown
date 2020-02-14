---
layout: post
title:      "Some tiny warnings when adding the Devise Gem to your application"
date:       2020-02-14 05:46:35 -0400
permalink:  tiny_warnings_about_devise_gem
---
Once you know how to implement a user authentication, it can be a bit tiring to do all the steps - to create the models, views, controllers, validations..., especially knowing that there is a gem that can do that for you. So Devise can handle all the user authentication as a whole. In only a few steps, the gem will create everything for you and you will just to customise it a little. 

In theory.

# What is happening with Devise?
Even though you know the authentication process, as a beginner, it is nice to know what will change after you installed Devise. In the gem documentation, they even warn that they do no recommend to use Devise if you don't have a good understanding of the Rails framework.

I think it is important in this case to read as much documentation as you can, as it will help you understand some "weird" things that happens during the process.

## Where are the controllers and models?
For instance, I was surprised to see that nothing appears in the user controller and models once you have installed the gem - because they are in meta controllers as part of the gem. Which means that if you want to modify, or customise your controller or model, you must absolutely know how it is supposed to look like, and what is supposed to be going on in it.
### What if I already created an authentication?
So what Devise do is basically the same thing that when you build a custom made authentication with a few exceptions if you did your own authentication system before installing the gem:  

*There is no need to delete your user model or controller before installing it, and you can keep your custom methods. The only thing that you need to know is that Devise has its own encrypting methods and is already using Bcrypt. Which means that you will have to [delete the password_digest attribute in your user model and the has_secure_password](https://stackoverflow.com/questions/38840292/argumenterror-when-using-devise-and-has-secure-password-together) in the model. For more information about how Devise uses Bcrypt : [How Devise keeps your Rails app passwords safe.](https://www.freecodecamp.org/news/how-does-devise-keep-your-passwords-safe-d367f6e816eb/)
*The Devise generator will recognise if you already have a User model and ```will add_columns``` to your model with a migration migration file automatically generated. You don't have to modify your user attributes in your first User migration file.

## Why are my user attributes not saved?
It also took me a few minutes to realise that some of my user attributes were not saved when creating a new user, while the logging in and out was working. So what was happening? 

In fact, in Devise, the ```password```, ```password_confirmation```, ```current_password``` and ```email``` (as the authentication key) are strong parameters. If you already have attributes in your user model, you will have to tell Devise that it is ok to use and save them. You will have to use the [ParameterSanitizer](https://www.rubydoc.info/github/plataformatec/devise/Devise/ParameterSanitizer) in the ApplicationController to permit specific parameters. If you want to look out for the question on the web, you can also look for [Strong Parameters](https://guides.rubyonrails.org/action_controller_overview.html#strong-parameters), wich are the reason why you need to use the ParameterSanitizer. 

I found this video tutorial to be a good one to understand how to deal with your custom attributes: [How to Add Custom Attributes to a Devise Based Authentication System by Jordan Hudgens](https://www.udemy.com/tutorial/professional-rails-5-development-course/how-to-add-custom-attributes-to-a-devise-based-authentication-system/)

## What is happening with my helper and validation methods?

The other thing is that you don't have to create authentication helpers as some are created for you :
*authenticate_user!
*current_user
*user_signed_in?
*sign_in(@user)
*sign_out(@user)
*user_session

These are the main helper methods that are usually helpful to manage the current user object in the controller and in the views. You will find more about it in [Devise's documentation](https://github.com/heartcombo/devise#controller-filters-and-helpers) and in this [Launchschool article](https://launchschool.com/blog/how-to-use-devise-in-rails-for-authentication).

# Enjoy Devise!

Learn.co provides a lot of resources about Devise, that I will share it in case you want to learn more:
*[Devise Lab](https://learn.co/lessons/devise_lab)
*[Devise architectures and modules](hhttps://learn.co/lessons/devise_readme)
*[Create roles with Devise](https://learn.co/lessons/rails-video-review-devise-roles-lab)

Obviously, I run into all these little issue when I first used Devise, as I didn't read all the documentation at the beginning. Some issues are not so simple to figure out quickly, some I hope this will save some people some times! At first I was a bit scared to use a gem for such a tricky subject, but the gem is widely used and makes developers' life so simple. Devise is a great tool, I hope you will enjoy it!