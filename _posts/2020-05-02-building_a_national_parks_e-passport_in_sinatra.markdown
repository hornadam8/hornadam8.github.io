---
layout: post
title:      "Building a National Parks E-passport in Sinatra"
date:       2020-05-02 19:26:52 +0000
permalink:  building_a_national_parks_e-passport_in_sinatra
---


 This application involved a lot of interesting back end challenges: implementing a data scraper to seed the parks model(from my data gem project), finding a way to display 62 park objects in a clean layout, building a search function for those objects, and adding google maps for each page. In addition, the other models (users and trips) need to be able to interact with the Park objects in the database so that User's "trips" would include information on the parks.
 
 So the database relationships in this case would be:
 
 
 Park -- has many Trips,
                has many Users through Trips
								
								
 User -- has many Trips,
                has many Parks through Trips
								
								
 Trip -- belongs to a Park
              belongs to a User
 
 
 So our Trip model will act as a join table for connecting Users with Parks. The next step was deciding on attributes for each model so I could write out validations for them in the models as well as the database migrations to create their related tables.
 

 The migrations included set the following attributes: 
 
 A user has a name, email and password_digest (bcrypt gem secures password). 
 
 Trips have a visit date, a review, a park_id and a user_id(since its a join table). 
 
 Parks have a lot of attributes which came from the original scraper (lots of descriptive strings) additionally, I modified the scraper to gather a photo from each page as well. 
 
 
 I set the database relationships and validated the attributes in each model (except user, for which i used brypt's has_secure_password, and validations could be handled by forms).  After migrating my database to create the tables, I inserted the scrapers into a helpers folder and modifyied the scraper to run without being called by the CLI and to create Park instances in my new model. Then I simply inserted the Scraper run call into my seeds and ran rake db:seed and instantly had my park objects persisted. 
 
 From there it was mostly downhill, setting up controllers and views. There was one interesting problem in setting up the park index which I wrote about in my previous post [How to Turn an Array into a HTML Grid Layout](/https://hornadam8.github.io/how_to_turn_an_array_into_a_html_grid_layout). 
 
 The finished product allows users to login, select a park from the index page or search for a park by name. Once on the page of a particular park, a user can add a trip to that park and leave a review. They can then view that trip on their profile page as well as in the park's sidebar. These trips have their own page as well where the user can edit or delete the trip. Check out the repo at the [Park Hopper Github page](http://https://github.com/hornadam8/park-hopper-app).
 
 
 
