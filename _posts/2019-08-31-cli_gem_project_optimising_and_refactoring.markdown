---
layout: post
title:      "CLI gem project: optimising and refactoring"
date:       2019-08-31 15:29:53 +0000
permalink:  cli_gem_project_optimising_and_refactoring
---


I "finished" my project around 7 days before the project delivery due date: the application was working, the navigation was defined, the text of each interface was carefully worded. 

## Introduction

I made an application who could scrape data from the PocketCamp wiki webpage and display these information in a CLI. PocketCamp is a mobile game in which you can built a little campsite and invite friendly animals in. These animals can sometimes give treats, such as building resources or what we call "essences" - basically a different kind of resource that make the game a little bit more difficult. You can find the code at this address: [Pocketcamp_guide_cli](https://github.com/AliceBrunel/pocketcamp-guide-cli/blob/master/lib/pocketcamp_guide_cli/animals.rb)

My CLI application is structured as below:
* with a Scraper class scraping data from the wiki webpage
* with an Animals class creating Animals objects from the data scraped by the Scraper class
* with a CLI class calling both classes to create the CLI application and to organise the navigation and UX

The application is based around 4 functionalities:
* It could print out a list of all the animal names
* It could retrieve animals from a search by name to get information about an animal
* It could retrieve animals from a search by resource to know whose animal can provide a specific resource
* It could retrieve animals from a search by theme to know whose animal can provide a specific essence

## First commits
At first, I created the main navigation interface, and pseudo-coded the result I was aiming for:
First, the text
```
def first_choices
	  puts "What would you like to do?"
	  puts ""
	  puts "To view the list of animals name type 0"
		puts "To search by animal name type 1"
		puts "To search by resource type 2"
		puts "To search by essence type 3"
		puts "To exit type exit"
		puts ""
```
Then for each option, I imagined a search method:
```
		input = gets.strip
		if input == "0"
		  print_all_animals
		  first_choices
		elsif input == "1" 
		  search_by_name
		elsif input == "2" 
		  search_by_resource
		elsif input == "3"
		  search_by_essence
		elsif input.downcase == "exit"
		  puts "See you on the campsite!"
		else 
		  puts "This input is not valid"
		  first_choices
		end
```

### First methods: search_by_name and find_by_name
I naturally started with the **find by name** method. I needed the app to find the object whose name was equal to the input given by the user and to get the object in return. I ended up with this method:
```
  def self.find_by_name(input)
    @@all.find {|animal| animal.name == input}
  end
```
@@all being a class variable storing all the Animals object in an array, I used the find method to find in each animal object wich animal object had an instance variable called "name" that was equal to the input given by the user. This method was working well and was returning the object each time it found and input equal to the object name. 

**In the CLI class**, I then only had to use the returned object in another method which could display the object information:
```
	def display_animal(animal_object)
	  puts "---|#{animal_object.name}|-----------"
	  puts ""
	  puts "   species: #{animal_object.species}"
	  puts "   It's theme and essence: #{animal_object.theme}"
	  puts "   This animal will give you: #{animal_object.resource}"
	  puts ""
	end
```

And created a **search method** taking in a user input and using it to display the result with the display_animal method:
```
  def search_by_name
    puts "Type a name or type back to return to the first choices"
    input = gets.strip.to_s.capitalize
    result = PocketcampGuideCli::Animals.find_by_name(input)
    if result != nil && input != "Back"
      display_animals(result)
      search_animal
    elsif input == "Back"
      first_choices
    else
      puts "Doesn't ring a bell... Try something else?"
      first_choices
    end
  end
```

This first attempt was working and made to return and display only one object.
### Next methods: search_by_resource and find_by_resource
I started then to work on the next method. To search by resource, I needed the application to find all the possible resources in order to list them, then print them out to show them to the user and help her select a resource, then to display the multiple results based of her search. It was a bit trickier in the sense that I had to create more that 2 methods, unlike what I previously did:
* I had to create a find method in the Animals class
* I had to create a search method in the CLI class
* I had to create a method listing the existing resources from the Animal instances
* I had to create a method displaying multiple animals with the CLI method display_animal

So, this is what I did. The code looked like that:
```
# In the Animal class, a find_by_resource to compare and base the search on the user input
  def self.find_by_resource(input)
    @@all.select {|animal| animal.resource.include?(input) == true}
  end
	
#In the CLI class, a search_by_resource using find_by_resource and the user input
def search_resource 
    PocketcampGuideCli::Animals.get_all_resources
    input = gets.strip.to_s.downcase
    array_result = PocketcampGuideCli::Animals.find_by_resource(input)
    display_search_results(array_result)
end

#In the Animals class, a get_all_resources, putting all the "resource" instance variable in an array

def self.get_all_resources
    array = []
    @@all.each do |animal|
      array << animal.resource
    end
    puts "Type the name of the item you are looking for from the list below:"
    puts "#{array.uniq}"
  end
	
#And a method to display multiple results
def multiple_result(array)
    array.each do |item|
      display_animals(item)
    end
end

```

This was working, so I started building the last method.

### Last method: search_by_essence and a refactoring opportunity
It was obvious that this last method was absolutely similar with the previous one. By I didn't want to lose track of what I was doing and willing to do, so I built the same serie of methods for the "essence" search. 

Once this was done, I took a step back and looked at all the methods to see where was the refactoring opportunities, in terms of technical implementation but also in terms of a UX optimisation:
* The search by name was allowing the user to receive only one result. But first, it was possible that the user would be looking for a character without knowing exactly the spelling of its name. So it would be an opportunity to have several results to widen the functionalities of the application. If the application can return several relevant results, it would make the application more accurate.
* The search by name could be based exactly like the other search methods. In this case, the application would be working around one unique find_by and search_by method which would make the code easier to read or to modify later.
* I wanted my application to follow the Don't Repeat Yourself precepts.

Then I had to find and describe the obstacle I created with my previous implementations:
* The search_by_name method, in addition with the single result issue, was capitalising the user input to make is correspond to the instance variable (names' first letter is always capitalise), while the other search methods were downcased.
* The search_by_name method didn't required to print a list of the possible choices, while this was the case with the other search methods (get_all_resources and get_all_essences).
* To avoid hard coding the search criteria ("name", "resource" or "essence"), I had to find a way to pass the criteria into the find_by and search_by methods.

## After refactoring
Here are the methods that resulted:
```
#In Animals cass, I created a find_by method using the search criteria and the input to compare them and return an array of objects in result:

def self.find_by(criteria, input)
	  @@all.select {|animal| animal.send(criteria).downcase.include?(input) == true}
	end
	
#The get_all_lists prints out the possibilities (all names, all resources or all essences). 

 def self.get_all_lists(criteria)
    array = []
    @@all.each do |animal|
      array << animal.send(criteria)
    end
    puts "Type the name of the item you are looking for from the list below:"
    puts "#{array.uniq}"
  end
	
#In CLI class, I created a search_by method using the search criteria, and a printall parameter to tell the app whether it should print a list of possibilities or not - please notice that the names of the functions have changed for a better lisibility:

 def search_by(criteria, printall)
    search_criteria = criteria
		# Although it could, I told the app not to display this list for object name research
    if printall == true
    PocketcampGuideCli::Animals.get_all_lists(search_criteria)
    end
    input = gets.strip.to_s.downcase
    array_result = PocketcampGuideCli::Animals.find_by(search_criteria, input)
    display_search_results(array_result)
  end

#Finally, I rewrote the first_choices method
	def first_choices
	  puts "What would you like to do?"
	  puts ""
	  puts "To view the list of animals name type 0"
		puts "To search by animal name type 1"
		puts "To search by resource type 2"
		puts "To search by essence type 3"
		puts "To exit type exit"
		puts ""
		
		input = gets.strip
		if input == "0"
		  print_all_animals
		  first_choices
		elsif input == "1" 
		  puts "Please type the name of the animal you are looking for, then press enter:"
		  search_by("name", false)
		elsif input == "2" 
		  search_by("resource", true)
		elsif input == "3"
		  search_by("theme", true)
		elsif input.downcase == "exit"
		  puts "See you on the campsite!"
		else 
		  puts "This input is not valid"
		  first_choices
		end
	end
	
```

# Conclusion
This implementation and refactoring improved the app by avoiding the use of 6 repetitive methods. It also made the logic more self explainatory and allows further modification and improvement of its functionality. This refactoring actually didn't take much time.
