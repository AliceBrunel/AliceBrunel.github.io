---
layout: post
title:      "Sinatra project: MenuCMS and what I learned"
date:       2019-10-15 09:46:34 +0000
permalink:  sinatra_project_menucms_and_what_i_learned
---


## Introduction 
The MenuCMS is a small application using Sinatra framework and offers to help small restaurant teams to submit menus ideas and choose a menu to publish to the restaurant webpage.

A restaurant team is composed of users, which have different roles. The users can either be Managers or Team Members. Each of them have the possibility to:
* Create a meal or a menu by posting a meal or menu idea
* View each of these meal or menu idea
* Update their own meal or menu ideas only
* Delete their own meal or menu ideas only
* They can access a profile page with the summary of their contributions.

**To represent the relations between all these elements, the application uses a Model View Controller design pattern**. 

## Environment
The application is based around a **config.ru** file to run the application, requiring the **environment** file. The environment is using Bundler with a few gems including the essential ones to allow the use of Sinatra, to use it with ActiveRecord and to communicate with the database.

Some gems and file are used for **testing and validation**:  `shotgun` and `pry` allow to run the application and see the modification practically in real-time. Pry pauses the application to allow to verify our structure at a specific event. 

The **Rakefile** contains a testing console started with Pry to test on real models, methods and data.

Some other gems add functionnalities: `sinatra-flash` is a flash message gem.

This environment was very useful and helpful to verify if, at any point, I was on the right track. I would usually implement something, git add, commit and push to my repository and then launch the application via Shotgun. Each time I would encounter an error, I would put a `binding.pry` where the error was located and would try to reproduce the process in Pry. This way, at each point, I was able to get the returned values (or error) and adapt my code. As soon as a functionality was corrected, I would push the correction and go to the next error.

## Database
### Connection with the database
The application is using ActiveRecord for storing information in a database. To establish the connexion, it uses the method to connect to a SQLite database:
```
# environment.rb 

ActiveRecord::Base.establish_connection(
  :adapter => "sqlite",
  :database  => "path/to/dbfile"
)
```

### Database design

The database design was one of the most intimidating part for me. The database needs to be perfectly conceived to make sure that the model relationships make sense for ActiveRecord. So, to me, it was important to have a good thought about the database design. I wrote [an article](http://https://alicebrunel.github.io/sinatra_portfolio_project_-_database_design) in my blog about my design process, which helped and was a good start, but wasn't complete. At some point, I realised I forgot a column, that I had to add later with a migration. 

I build the following schema for my app:
```
# This schema is automatically created by Rake when requiring $ rake db:create

ActiveRecord::Schema.define(version: 2019_10_11_133438) do

  create_table "meals", force: :cascade do |t|
    t.string "name"
    t.string "ingredients"
    t.string "method"
    t.integer "user_id"
  end

  create_table "menu_meals", force: :cascade do |t|
    t.integer "menu_id"
    t.integer "meal_id"
  end

  create_table "menus", force: :cascade do |t|
    t.string "name"
    t.integer "user_id"
    t.integer "activated"
  end

  create_table "users", force: :cascade do |t|
    t.string "username"
    t.string "email"
    t.string "password_digest"
    t.string "role"
  end

end
```

And created a folder with the corresponding models. To make sure the migrations are done before using the application, it is recommended to add a method to raise a message if migrations are pending in the config.ru file. 
```
# config.ru

if ActiveRecord::Base.connection.migration_context.needs_migration?
  raise 'Migrations are pending.'
end
``` 

## Building CRUD functions
### Configurations and helper methods
This application offers the possibility to create, read, update and delete through the application controllers. To do so, I implemented an application controller with `Sinatra::Base` for the first couple of routes, with some configurations and helper methods. 

The configurations where to set and enable sessions for secure log in and to enable Sinatra Flash gem methods:
```
  configure do
    set :public_folder, 'public'
    set :views, 'app/views'

    enable :sessions
    set :session_secret, "menuscollection"

    register Sinatra::Flash
  end
```

The helper methods were to facilitate the manipulation and query of the session via the user:
```
helpers do

    def logged_in?
      !!current_user
    end

    def current_user
      @current_user ||= User.find_by(id: session[:user_id]) if session[:user_id]
    end

  end
```
This way is was easier to manipulate the data and interface depending of who was the current user and if the user was correctly logged in. 

### A controller for each model
For each model, I created a controller, inheriting from the application controller, and where I put the CRUD functionalities. To allow the application to use them, especially  `patch` and `delete` it is necessary to specify them in the config file with  `Rack::Methodoverride` :
```
# config.ru 

use Rack::MethodOverride
use UsersController
use MealsController
use MenusController
run ApplicationController
```

These controllers really are the fun part: this is where you structure the application and give it a life and a meaning. I made sure in each route that I checked that the user was logged in with the helper methods, otherwise the user would be redirected to the sign in page. If the user wanted to edit or delete something, I had to make sure first that this was its own thing. So, for instance, I created this type of routes:
```
# menus_controller.rb

  get '/menus/:slug/edit' do
    if logged_in?
    @menu = Menu.find_by_slug(params[:slug])
      if @menu && @menu.user == current_user
        erb :'menus/edit'
      else
        redirect to '/menus'
      end
    else
      redirect to '/signin'
    end
  end
```
Here, the application check that the user is logged in and if so, if this user is also the creator of the menu, then the user can access the update functionalities. If not, he is redirected to the previous page. 

For instance, in this use case, it is not clear for the user if the redirection is the consequence of a bug or if it was meant to be.  To help the user communicate with the application, there is two possibilities here:
* you can use a message telling the user "you don't have the right to do that"
* or you can give the user the possibility to do something only when it is actually possible 

### Ruby templating with ERB

I thought it would be interesting to hide the Edit and Delete buttons for any other user than the creator. Thanks to ERB (Embeedded RuBy) it is possible to add methods in HTML files. It was fun to implement some conditional methods directly in the interface to make the application more dynamic. For instance, with the previous use case, I was able to do this:
```
# Here, in this erb file, the delete button is simply hidden if the current user isn't also the creator.

<% if @creator == current_user %>
  <a class="small-button" href="/menus/<%= @menu.slug %>/edit">Edit this menu</a>
    <form method="POST" action="/menus/<%= @menu.slug %>/DELETE">
      <input type="hidden" name="_method" value="DELETE">
      <input  type="submit" value="Delete" class="delete-button">
    </form>
  <% end %>
	
	# And in the menus_controller file
	
	  get '/menus/:slug' do
    if logged_in?
      @menu = Menu.find_by_slug(params[:slug])
      @creator = @menu.user
      erb :'menus/show'
    else
      redirect to '/signin'
    end
  end
```
It made it really easy to have a flexible application corresponding to the user's models.

## Presentation of the application 
Here is the application in action:

<iframe width="560" height="315" src="https://www.youtube.com/embed/jNBcyrN1m3c" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>

Thank you for reading!
