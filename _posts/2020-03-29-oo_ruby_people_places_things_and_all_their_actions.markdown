---
layout: post
title:      "OO Ruby: People, Places, Things and All Their Actions"
date:       2020-03-29 13:23:15 +0000
permalink:  oo_ruby_people_places_things_and_all_their_actions
---


I really love object oriented ruby because when written properly, you can almost visualize your code as a physical object. The way I think about object oriented ruby is through the scope of basic grammar. 

A class, which defines a type of object, is essentially a common noun. It could be describing cats, rocks, cities, or three-legged stools. The purpose of a class is to describe, in code, what an object is and what it can do. It is also responsible for describing how each instance of that object is made through its #initialize (instance) method.

An instance of a class is like a proper noun. It could be describing Garfield, El Capitan, Los Angeles, or stool #5 at Hole-in-the-wall Bar and Grill. Each instance has attributes and functions(instance methods) that are determined by it's class.

Methods are like verbs, and their scope(class and instance) are determined by whether the object "doing" the method is singular or plural(just like subject/verb agreement). For example, if Garfield(one cat) were going to change his name to Tom, you would need to build an instance method in your Cat class to accomplish that(i.e. #Garfield.change_name). But if you wanted every cat to meow at once, or if you wanted every city to pass a new recycling law, you would need a class method(i.e. #Cat.meow, #City.recycle (looks like an instance method except it includes "self" since it is built inside a class). 

Once you combine all these concepts, your are essentially defining a world of objects that can interact with each other to perform various functions cooperatively. Just like real-life objects.
