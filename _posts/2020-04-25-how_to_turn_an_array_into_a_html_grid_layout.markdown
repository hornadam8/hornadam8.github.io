---
layout: post
title:      "How to Turn an Unwieldy Array into a Clean HTML Grid Layout in ERB"
date:       2020-04-25 14:18:12 -0400
permalink:  how_to_turn_an_array_into_a_html_grid_layout
---


I spent the last week building out my sinatra project called Park Hopper. The idea was to build a sort of online national park passport where users can view all national parks and add trips with reviews and visit dates which could be seen on their profile as well as the park page side bar. One of the challenges I found most interesting was figuring out how to display the attributes of 62 park objects in grid rows of 3 as opposed to a list. Here's the solution I found:

> class ParksController < ApplicationController
 
>  get "/parks" do
>  
>      @user = User.find(session[:user_id])
>      @parks = Park.all
>      @chunks = @parks.each_slice(3).to_a
>      @i = 1  
>      erb :"parks/index"
> end
> 
> ...

So I assign the user after authentication in a separate controller and use an instance method to create an array of all park objects. #@i is my counter for setting the class item number in my erb file(we'll get there). And by using #each_slice(3).to_a I turned my array into an array of arrays each containing 3.... oh except 62 isn't divisible by 3, so the last one contains only 2. This presented an additional hurdle for the view...

>  <%@chunks.each do |park|%>
> 
>      <div class="item<%=@i%>">
          <img src="<%=park[0].image%>"
          width="307"
          height="217">
          <h2><%=park[0].name%></h2>
          <p><%=park[0].description%></p>
          <a class="btn" href="parks/<%=park[0].id%>" class="btn">View park page/add a trip!</a> </div>
          <%@i += 1%>
        <div class="item<%=@i%>">
          <img src="<%=park[1].image%>"
          width="307"
          height="217">
          <h2><%=park[1].name%></h2>
          <p><%=park[1].description%></p>
          <a class="btn" href="parks/<%=park[1].id%>">View park page/add a trip!</a> </div>
          <%@i += 1%>
        <div class="item<%=@i%>">
        <%if park[2]%>
          <img src="<%=park[2].image%>"
          width="307"
          height="217">
          <h2><%=park[2].name%></h2>
          <p><%=park[2].description%></p>
          <a class="btn" href="parks/<%=park[2].id%>">View park page/add a trip!</a> </div>
        <%@i += 1%>
            <br>
        <%end%>
>       <%end%>
>       

So thats a lot to look at, but all it does is use the counter and the chunks array and create a grid item with an image, name and description for each park in the sub-arrays of 3. It also uses a conditional to ensure that there is a 3rd item before rendering it. This logic along with the grid css/html guide at: https://www.w3schools.com/cssref/tryit.asp?filename=trycss_grid-template-columns helped me turn a large array into a nice clean grid layout. 
			
			
			


