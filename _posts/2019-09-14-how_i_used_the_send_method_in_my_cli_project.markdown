---
layout: post
title:      "How I used `send` to call on an object's attribute"
date:       2019-09-14 12:23:08 -0400
permalink:  how_i_used_the_send_method_in_my_cli_project
---


The CLI project I was working on is an application where the user is given 3 options, giving back 3 different formatted outputs but using one method. I didn't want to hard code 3 methods so I had to think about a solution to get the user input to reflect the method parameters. The only way to do that was by using the `send` method.

## How I used it
Let's say I wanted to select and return all the object which name, or color, or type corresponds to the user input.
```
def options

  user_choice = gets.strip
	
	if user_choice == "name"
	  search_by_name
	elsif user_choice == "type"
	  search_by_type
	elsif user_choice == "color"
	  search_by_color
	else
	   puts "Not a good choice"
	end
	
end
```

In this case, I have to built 3 different methods:
```
def search_by_name
  puts "Type a name"
  user_input = gets.strip

  result = find_by_name(user_input)
  a_method_to_format_the_result(result)
	
end

def find_by_name(input)
  @@all_objects.select { |object| object.name.include?(input)}
end
```
But would have to do the same for color and type and I would end up with 6 methods.

Instead, with `send`, I was able to do this:
```
def search_by(criteria)

user_search_input == gets.strip

result = find_by(criteria, user_search_input)
a_method_to_format_the_result(result)
	
end

def find_by(criteria, input)
  @@all_objects.select { |object| object.send(criteria).include?(input)}
end
``` 
And then, adjust the choices:
```
def options
  # The user input become the criteria
  criteria = gets.strip
	
	if criteria == "name" || criteria == "color" || criteria == "type"
	  search_by(criteria)
	else
	   puts "Not a good choice"
	end
	
end
```

## More about send

I don't know at first the value of `criteria` because it is the user's choice, but I known that it must correspond to one of the object attribute. In the object class, when I write:
```
attr_accessor :name, :color, :type
```
It is the same as if I wrote:
```
def name
  @name
end

def color
  @color
end

def type
  @type
end
```
All I want to do is to call a method to get the value of the object's attribute, to return it to the user. Therefore, the method I have to call is wether name, or color, or type.Therefore, send is the answer as it transforms the `criteria` into a method call that returns the value of the object's attribute.

Et voilà!

