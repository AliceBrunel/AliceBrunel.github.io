---
layout: post
title:      "Installing Bootstrap in a Rails application"
date:       2020-02-10 05:46:35 -0400
permalink:  installing_bootstrap_with_rails
---

# Introduction 

Just a quick reminder about what is Bootstrap :
Bootstrap is a **front-end framework** that you can use to quickly implement a responsive website layout. It comes with HTML, CSS and Javascript files.

# Why should I use the Bootstrap Ruby Gem?

This is not required to use a Gem to use Bootstrap on a Rails application. 

In fact, you can simply copy-paste the stylesheet and Javascript script links that Bootstrap team shares with you and place it accordingly with the **[documentation](https://getbootstrap.com/docs/4.4/getting-started/introduction/)**.

The caveat here is that when you rely on external scripts, the style of you application will depend on them in a way that if the website they are on ever go down, all the style on your site will disapear. Also, if they change anything in they style, and that you have created your own style based on this, your style will break.

So, it is considered [bad practice](https://rails.devcamp.com/trails/dissecting-rails-5/campsites/using-rubygems/guides/how-to-install-bootstrap-4-rails-5-application) to do so. Instead, you can install the Bootstrap Ruby Gem.

# Installation 

On the Bootstrap page, there is a section about Ruby Gem. Unfortunately, this section is rather succint and it is better to refer to the [gem page on Github](https://github.com/twbs/bootstrap-rubygem).

## Adding to the Gemfile
Go to the gem readme and copy the code to add to your Gemfile :

```gem 'bootstrap', '~> 4.4.1'``` 

## Check in the gemfile.lock the version sprockets-rails
Then they say that you should ensure that `sprockets-rails` version is the latest version. 

Open your Gemfile.lock, and look for the version of `sprockets-rails`, which will be under `rails`. Do NOT modify directly the Gemfile.lock if you realise that you don't have the good version, but this shouldn't be necessary with the latest rails versions.


## Installing with bundler
Close your running server and run bundle install :

```$ bundle install```

## Import Bootstrap style
### rename the application.css file
Once you get the installation confirmation, look for the application.css file in `app/assets/stylesheets` and rename it to application.scss. Depending on how many models you created, **you will have to rename the extension of all your .css files to .scss**.

### Insert the @import bootstrap
Under the commented text (you can also delete it), copy-paste the import variable in each of your .scss files :

```@import "bootstrap";```

## Javascript adding 
If you haven't added jquery gem yet, put it in your Gemfile :

```gem 'jquery-rails'```
And `bundle install`. Then, in your javascripts folder in app/assets, look for the application.js file, and paste the following in this specific order:
```
//= require jquery3
//= require popper
//= require bootstrap-sprockets
```
Then this is it, enjoy designing your Bootstrapped website!