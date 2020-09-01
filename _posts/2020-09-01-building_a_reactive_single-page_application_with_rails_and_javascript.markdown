---
layout: post
title:      "Building a Reactive Single-Page Application with Rails and Javascript "
date:       2020-09-01 10:07:30 +0000
permalink:  building_a_reactive_single-page_application_with_rails_and_javascript
---


This week I redesigned my Park Hopper project to be a more reactive single page application using Javascript to animate the front-end.  This required a few steps:  First, I would need to set up the seed generator from the park data scraper. Next, I would need to design my controllers to render and accept JSON to and from my scripts. Finally, I would need to build out a Javascript object models for Parks and their comments, as well as write a script to handle more general event-firing.

The first part was relatively familiar, I created to helper files containing the data scrapers and added a #run method to generate the park instances. Then, by calling Scraper.run in my seed file, I saved all the instances to the database to later be rendered by the controllers as JSON. Without getting too gritty with the specifics of the scraper, here's the run method that brings it all together: 

```
def self.run
    self.make_parks
    ParkScraper.all.each do |park|
      Park.create(park.instance_values)
    end
  end
	```
	
When make parks is called, the park instances are created from the NationalParks.org site to be saved as Rails model objects in the iteration that follows.

With some basic content out of the way, next was creating controller actions to handle API requests. The first and most obvious was the parks index action, to render all the parks. 

```
def index
    parks = Park.all
    render json: parks,include: [:comments], except: [:information,:weather,:nearby_parks,:wildlife,:tours_and_camping,:link,:created_at,:updated_at]
  end
```

I chose to include the associated comments, along with the park's photo and description, but opted to leave out the more long form information for the purpose of keeping the single page from being overwhelming. Next I needed to have some scripts with fetch requests to bring this info across to the frontend.

I started by creating a Park model which essentially repeated the flow of the scraper.run method for creating park instances and div cards:

```
static get(){
    fetch(parksUrl)
    .then(r => {return r.json()})
    .then(data => Park.make(data))
  }

  static make(data){
    let parks = data.map(park => new Park(park.id,park.image,park.name,park.description,park.comments,park.likes,park.like_status));
    Park.render(parks)
  }

  static render(parks){
    parks.forEach(park => Park.renderCard(park));
    let likeGlyphs = document.querySelectorAll('.like-glyph');
    for (let glyph of likeGlyphs){
      glyph.addEventListener("click",likeCallback)
    }
  }
	```
	
	This flow fetched the JSON for all park objects, creates their adjacent Javascript objects, then iterates through them rendering a card and setting an event listener to the like glyph for each park instance. The #renderCard function creates the individual DOM elements for each featured park attribute and sets additional event listeners specifically targeted at the comments:
	
	```
	static renderCard(park){
    let parkCard = document.createElement('div');
    parkCard.className = "card";
    parkCard.dataset.id = park.id;
    parkCard.innerHTML = `
    <img src =${park.image} width="300" height="150">
    <h5>${park.name}</h5>
    <p>${park.description}</p>
    <ul>
      <li class="like"><span class="like-text">Likes: ${park.likes}</span><span class="like-glyph">${park.likeStatus}</span></li>
    </ul>
    <button class='comments_toggle' id='comments_toggle'>comments</button>
    <br>
    <div class='comment_container' display="none">
      <ul>
      </ul>
    </div>
    <br>
    <form id="comment-form" class="form" display="none" action="">
        <input type='text' name="comment" id="comment-input" cols="30" rows="10">
        <br>
        <button id='submit'>submit</button>
    </form>
    `
    console.log(parkCard.getElementsByClassName('like')[0])
    if (park.likeStatus == '♥'){
      parkCard.getElementsByClassName('like-glyph')[0].classList.add('activated-heart')
    }

    let commentContainerList = parkCard.getElementsByClassName('comment_container')[0].getElementsByTagName('ul')[0];
    Comment.makeFromDb(park.comments,commentContainerList);

    let commentForm = parkCard.getElementsByClassName('form')[0];
    commentForm.style.display = "none";

    let commentContainer = parkCard.getElementsByClassName('comment_container')[0];
    commentContainer.style.display = "none";

    Comment.toggleShow(parkCard);

    commentForm.addEventListener('submit',function(e){
      e.preventDefault();
      let data = {
        content: e.target.comment.value,
        park_id: e.target.parentNode.dataset.id
      }
      Comment.formSubmit(data);
      this.getElementsByTagName('input').comment.value = ""
    });
    let cards = Array.prototype.slice.call(document.getElementsByClassName("card"));
    if(!cards.some(e => {return e.dataset.id === parkCard.dataset.id})){
      main.appendChild(parkCard)
    }
		}
		}
		```
		
		This accomplished my goal of creating a reactive single page version of Park Hopper.
