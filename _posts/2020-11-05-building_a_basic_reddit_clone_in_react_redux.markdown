---
layout: post
title:      "Building a basic Reddit clone in React/Redux"
date:       2020-11-05 10:42:30 +0000
permalink:  building_a_basic_reddit_clone_in_react_redux
---


For my final project, I decided to build a Reddit clone using the React and Redux javascript libraries. I called my application Grouper and the structure consisted of groups (subreddits) each containing forms and containers for posts which had their own forms and containers for comments.

The first thing I needed to do was create an outer-most group container.  This component was responsible for connecting to the redux store, getting information on each group and displaying that information. Additionally, it needed to pass props down to the form specifically a dispatch to add new groups. 

With that out of the way, the next step was building out a basic form component to take in inputs and pass them off to the reducer and send our data to the backend to be persisted. Next, I created a presentational component for a single group and called it within an iterator in the groups container to be displayed. Finally, to move on to the neying xt step, I need to create a show page component and link so that groups could be posted to and viewed individually. 

It was at this point that I hit my first real snag. When I first set up the show page, clicking the link would update the url, but did nothing to update the DOM. After toying with different solutions I realized that I simply needed to place my show page route above the route for the index in order for the full URL to be recognized.

After bypassing that hurdle, I went on to create the posts container that would be fitted to each group show page. When creating the route for the post show page I was mindful to place it at the top of my router to avoid the issues I encountered with groups. The structure for posts closely followed that of groups and ultimately went pretty quickly.

Finally, I created one more layer of nested components by adding comments which mirror the structure of the others but lacked a show page.  With the basics built out my simple Reddit clone was complete.
