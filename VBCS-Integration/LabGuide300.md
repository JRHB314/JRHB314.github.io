![](images/Picture-lab300.png)  
Updated: November 6, 2018

## Introduction

Now we will try connecting to a non-Oracle Cloud Database; in this case, Google's Firebase. 

**_To log issues_**, click here to go to the [github oracle](https://github.com/oracle/learning-library/issues/new) repository issue submission form.

## Objectives

- Learn how to set up a Firebase Database
- Connect to VBCS
  - Display information from Database
  - Request specific data

## Required Artifacts

- Cloud trial account
- Google account

# Lab 300

## Connecting to Firebase Database

### **STEP 1**: Set Up Database

<b>Create Firebase Account</b>

Visit [Firebase's website](https://firebase.google.com/). On the top right corner, click `Sign in`.

![](/images/lab300/300-1.png)<br>

Your Firebase account is automatically linked with your google account, so you can sign in with your gmail account credentials to get started. If you don't have one, sign up for one and then revisit the Firebase website. Once logged in, click `Get Started`.

![](/images/lab300/300-2.png)<br>

You are now on the main dashboard page and can move on to the next section.

![](/images/lab300/300-3.png)<br>

<b>Create Realtime Database</b>

Click `Add Project`. Choose a name, leave the default settings for location, make sure all three boxes are checked, then hit `Create Project`.<br>

![](/images/lab300/300-3-4.png)<br>

It will take about 10 seconds or so to create the project. The page should then redirect you to your Database Home Page. Note that as it is, we have only just made our database; there is no actual data in our database yet.<br>

![](/images/lab300/300-3-5.png)<br>

The first thing that we need to do is to `edit the security rules of our database to allow read/write access`. Because this is a trivial database, we can assume that it doesn't necessarily need to be secure. For something like an enterprise-level project, however, we'd want more specific rules to be created. Google has documentation for that [here](https://firebase.google.com/docs/database/security). For now, go to the `Rules` tab and simply change `read` and `write` to `true`.

<!-- [here (https://firebase.google.com/docs/database/security).<br> -->

<b>Populate Database</b>

Inside this GitHub repository, navigate to the `resources` directory and download the `bookList.json` file. Open it inside your preferred text editor. Note the structure is of several book objects identified by ISBN. <br>

![](/images/lab300/300-3-6.png)<br>

Go back to the Data tab of your Database. Near the top right, hit the three dots dropdown, then `Import JSON`.<br>

![](/images/lab300/300-3-7.png)<br>

Import the `bookList.json` file.<br>

![](/images/lab300/300-3-8.png)<br>

Your database should populate with the information from the file.<br>

![](/images/lab300/300-3-9.png)<br>

To test that everything is set up correctly, enter the shown url for the Database into a browser with `/books.json` appended to the end of the URL.<br>

![](/images/lab300/300-3-10.png)<br>

```
https://projectname-XXXXX.firebaseio.com/books.json
```
A list of the books and all their info should be shown. <br>

![](/images/lab300/300-3-11.png)<br>

<b>Side Note</b>: If the formatting of your data looks different, add the [JSON Viewer extension](https://chrome.google.com/webstore/detail/json-viewer/gbmdgpbipfallnflgajpaliibnhdgobh?hl=en-US) to your Chrome browser.

### **STEP 2**: Loading Book Data on VBCS

<b>Create New Page</b>

The first thing we want to do is create another page on which we'll display our book descriptions/images. Let's call this page `book-catalog`. To make this page, right click on `main-start` and hit `Duplicate`. Then, right click on the `copy page` to rename it to `book-catalog`. On the `Design` view of the page, click on the `Welcome to the Home Page` heading, then hit the trash can icon in the bottom left corner of the right-side bar to delete the component. Your page should look like:<br>

<br>![](/images/lab300/300-3-25.png)<br>

Now we have to update our tab bar to include this new page that we just made. Go to the `Code` view for the page, and look for the `"oj-tab-bar-XXXXXXXXX-X"` item. Inside that, you should see two `oj-tab-bar-XXXXXXXXX-X-tab-X` items. Copy the code for the first tab (the one with dull formatting) and paste it right below the code for the second tab. Rename the tab to `Catalog` and change the listener to `clickCatalogTab` (this event doesn't exist yet, but we'll make it soon). Finally, change the first tab's style to bright, so that only the third tab is dull.<br>

![](/images/lab300/300-3-26.png)<br>

<b>Note:</b> In the previous step, you could have gone to customize the tab bar on the Design view and hit the plus sign to the right of the title `Tabs` in the customization bar, but this would not have copied the style or the listener. For this reason, we decided to manually copy the code in ourselves.

<br>
Repeat this process for the other pages. On the other pages, the `Catalog` tab should have bright styling.<br>

![](/images/lab300/300-3-27.png)<br>

Now we just need to create our action chain `navigateCatalogPage` (created at the flow level) and our event `clickCatalogTab` (created for each page) and we are good to go. Double check that you can navigate between all three pages.

<b>Add HTML/CSS</b>

At this point, our database has been set up and we have our third page created with its navbar. Next, we'll be using our database information to populate the third page with images and descriptions of books. The first thing we need to do now is to come up with a layout of how we want our page to look. For this lab, we'll format the page with a left-side column to display book images and a right-side column to display the book information.<br>

Let's create this layout by adding the HTML structure to our new book-catalog page. Navigate to the `Code` view of the page, and copy and paste this HTML code and add it at the very end:

```
<div class="row">
   <div class="column"> <div id="leftColumn"></div> </div>
   <div class="column"> <div id="rightColumn"></div> </div>
</div>
```

With the HTML in place, we can next add the css for the two columns to style them properly:

```
  .column {
    float: left;
    width: 35%;
}

.row:after {
    content: "";
    display: table;
    clear: both;
}
```

![](/images/lab300/300-3-d1.png)<br>

With these 2 div objects properly set up, we'll be able to identify where the javascript should populate the images and descriptions. Let's move on to the actual javascript function that will populate our page.<br>

<b>Create Javascript Function</b>

VBCS requires that functions be written in a very particular way. You will see the base outline for this already here.<br>

![](/images/lab300/300-3-14.png)<br>

The outermost function will return a `PageModule object` to VBCS; it sends all of the module functions we create to VBCS so we can more easily access them in other components. Each module can be treated like a separate Javascript file.<br>

To define a module, use this format:
```
PageModule.prototype.functionName = function () { OUR CODE HERE };
```
Our functions will look like:
```
define([], function() {
'use strict';

var PageModule = function PageModule() {};
PageModule.prototype.functionOne = function () { OUR CODE HERE };
PageModule.prototype.functionTwo = function () { OUR CODE HERE };
PageModule.prototype.functionThree = function () { OUR CODE HERE };

return PageModule;
});
```
To get started, let's set up the module that will load the book descriptions. <br>
```
define([], function() {
'use strict';

var PageModule = function PageModule() {};

PageModule.prototype.loadDescriptions = function () {
  //code here
};

return PageModule;
});
```
Then we want to grab the rightcolumn so that we can append elements to that part of the page. Put this in the "code here" section.
```
const app = document.getElementById('rightColumn');
```
Now we are ready to make our GET request to our database. Make sure to replace the url below with the url for your database.
```
var request = new XMLHttpRequest();
request.open('GET', 'https://projectname-XXXXXX.firebaseio.com/books.json', true);
```
We want to peform some actions once this request is made.
```
request.onload = function () {
//actions to perform once request is made
}
```
Before we can do anything with the response, we have to parse it as a JSON. Put this code inside the request.onload function.
```
var data = JSON.parse(this.response);
```
Right below that, put this code. It will run desired actions if the request is a success, and return an error if there's a problem.
```
if (request.status >= 200 && request.status < 400) {
//actions to perform on successful request
}
else {
const errorMessage = document.createElement('marquee');
errorMessage.textContent = "Request failed.";
app.appendChild(errorMessage);
}
```
Next we are going to run through the children of the JSON response and add each entry as a line on our webpage. We'll also add a horizontal rule between each, and use a blank image to add some space between book descriptions.
```
    if (request.status >= 200 && request.status < 400) {
      Object.keys(data).forEach(result => {
        const line = document.createElement('hr');
        app.appendChild(line);

        const title = document.createElement('p');
        title.textContent = data[result].title;
        app.appendChild(title);
        const author = document.createElement('p');
        author.textContent = data[result].author;
        app.appendChild(author);
        const ISBN = document.createElement('p');
        ISBN.textContent = result;
        app.appendChild(ISBN);
        const genre = document.createElement('p');
        genre.textContent = data[result].genre;
        app.appendChild(genre);
        const published = document.createElement('p');
        published.textContent = data[result].publish_date;
        app.appendChild(published);
        const publisher = document.createElement('p');
        publisher.textContent = data[result].publisher;
        app.appendChild(publisher);

        const space = document.createElement('img');
        space.src = "https://i.imgur.com/gAYM6Ws.png?3";
        app.appendChild(space);
      });
    }
```
Finally, all together:
```
PageModule.prototype.loadDescriptions = function () {

    const app = document.getElementById('rightColumn');      

    var request = new XMLHttpRequest();
    request.open('GET', 'https://asset-bdf37.firebaseio.com/results.json', true);

    request.onload = function () {
      // Begin accessing JSON data here
      var data = JSON.parse(this.response);
      if (request.status >= 200 && request.status < 400) {
        Object.keys(data).forEach(result => {
          const line = document.createElement('hr');
          app.appendChild(line);

          const title = document.createElement('p');
          title.textContent = data[result].title;
          app.appendChild(title);
          const author = document.createElement('p');
          author.textContent = data[result].author;
          app.appendChild(author);
          const ISBN = document.createElement('p');
          ISBN.textContent = result;
          app.appendChild(ISBN);
          const genre = document.createElement('p');
          genre.textContent = data[result].genre;
          app.appendChild(genre);
          const published = document.createElement('p');
          published.textContent = data[result].publish_date;
          app.appendChild(published);
          const publisher = document.createElement('p');
          publisher.textContent = data[result].publisher;
          app.appendChild(publisher);

          const space = document.createElement('img');
          space.src = "https://i.imgur.com/gAYM6Ws.png?3";
          app.appendChild(space);
        });
      }
      else {
        const errorMessage = document.createElement('marquee');
        errorMessage.textContent = "Request failed.";
        app.appendChild(errorMessage);
      }
    }

    request.send();
  };
```
Careful with your brackets here; it's easy to get one too many or one too few.<br>

<b>Calling Functions on Page</b>

We want this `loadDescriptions` function to be called whenever the page loads. Go to `Events` on the left sidebar for the Catalog page.<br>
Click `Create Event Listener`, then under `Lifecycle Events`, select `vbEnter`. This will be an event that runs whenever the page loads.<br>
![](/images/lab300/300-3-15.png)<br>
Hit the + sign next to Page Action Chains to create a new action chain. Name this runLoadDescriptions.<br>
![](/images/lab300/300-3-16.png)<br>
Click on the name of your event, then on the right side hit the link to open the action chain editor.<br>
![](/images/lab300/300-3-17.png)<br>
Drag "Call Module Function" onto the plus sign.<br>
![](/images/lab300/300-3-18.png)<br>
Select Module Function. You should see a Page Function named `loadDescriptions` in the list. Select it, and you should be good to go.<br>
![](/images/lab300/300-3-19.png)<br>
Test the page, and the books should appear on the Catalog page. <br>
![](/images/lab300/300-3-20.png)<br>
With the loadDescriptions function done, we are going to create our next function, `loadImages`. The process is basically the same, except we are appending images instead of text.<br>
Insert this code alongside the first module:

```
PageModule.prototype.loadImages = function() {
  const app = document.getElementById('leftColumn');

  var request = new XMLHttpRequest();
  request.open('GET', 'https://asset-bdf37.firebaseio.com/results.json', true);

  request.onload = function () {
    // Begin accessing JSON data here
    var data = JSON.parse(this.response);
    if (request.status >= 200 && request.status < 400) {
      Object.keys(data).forEach(result => {
        const bookCovers = document.createElement('img');
        bookCovers.src = data[result].image_url;
        console.log(result);
        app.appendChild(bookCovers);
        const p = document.createElement('p');
        p.textContent = "\n";
        app.appendChild(p);
      });
    }
    else {
      const errorMessage = document.createElement('marquee');
      errorMessage.textContent = "Request failed.";
      app.appendChild(errorMessage);
    }
  }

  request.send();
};
```

Add another action change under vbEnter, this one called `runLoadImages`. Set it up the same as runLoadDescriptions, with this one calling the loadImages module.<br>
Test the page one more time, and we should see the book covers to the left of the book descriptions. <br>
![](/images/lab300/300-3-21.png)<br>
Great job!

### **STEP 3**: Searching Books on VBCS

<b> Set Up Search Page </b>

Let's review what we've done until this point. So far, we've built our web application, created a Firebase database and populated it with information, and wrote custom Javascript to extract data from our database URL. We invoked those functions and had them run at page load time, and we were able to display book images and descriptions on our catalog page. Great! But what if we want to display books based on a user search? That takes a bit of extra work. We'll need to first capture the user's input, and then parse our JSON object accordingly.<br>

First create a third page for this website's search functionality. We'll call it `search`. Duplicate `main-start` and rename the copy `search`.<br> 

Change `Welcome to the Home page` to say `Search`. Drag and drop a `user input` box for the user to type in their search term, followed by a `button` for running that search. Click on the `Input Text` label and change it to say `Genre:`. Let's also drag over a `button` to the right of the input text. Change the text of the button to `search`.<br> 

![](/images/lab300/300-3-ds3.png)<br>

Note, however, that we only have three tabs; we need to make one more tab for the new page.<br>

Briefly:<br>
  * Copy and paste code for a new tab in each page.
  * Change the tab name to "Search" and the onclick listener to clickSearchTab.
  * Create an action chain navigateSearchPage at the flow level.
  * Create an event listener on each page called clickSearchTab.

<i>Review Step 2 for more specific instructions.</i> 

![](/images/lab300/300-3-30.png)<br>

Now that we've finished our simple layout, we need to save the user's input into a variable. On the left side click the `(x)` icon to open up `Variables` page. Create a new variable and call it `genre`.<br>

![](/images/lab300/300-david-search-5.png)<br>

Go back to the search page and click on the text input box. Under `Data`, enter `{{ $page.variables.genre }}`. This saves the value that the user types into our genre variable.

![](/images/lab300/300-david-search-6.png)<br>

<b> Create Search Function</b>

Next, let's copy over the Javascript code. Under the `JS` tab of our catalog page, copy and paste the two slightly modified functions below onto our search page. 

```
 PageModule.prototype.loadDescriptions = function (inputGenre) { // our function now takes in a "genre" input

      const app = document.getElementById('rightColumn');      

      var request = new XMLHttpRequest();
      request.open('GET', 'https://asset-bdf37.firebaseio.com/results.json', true);

      request.onload = function () {
        // Begin accessing JSON data here
        var data = JSON.parse(this.response);
        if (request.status >= 200 && request.status < 400) {
          Object.keys(data).forEach(result => {     
            if(data[result].genre == inputGenre){ // we'll only want to display descriptions for a specific genre
              const line = document.createElement('hr');
              app.appendChild(line);

              const title = document.createElement('p');
              title.textContent = data[result].title;
              app.appendChild(title);
              const author = document.createElement('p');
              author.textContent = data[result].author;
              app.appendChild(author);
              const ISBN = document.createElement('p');
              ISBN.textContent = result;
              app.appendChild(ISBN);
              const genre = document.createElement('p');
              genre.textContent = data[result].genre;
              app.appendChild(genre);
              const published = document.createElement('p');
              published.textContent = data[result].publish_date;
              app.appendChild(published);
              const publisher = document.createElement('p');
              publisher.textContent = data[result].publisher;
              app.appendChild(publisher);

              const space = document.createElement('img');
              space.src = "https://i.imgur.com/gAYM6Ws.png?3";
              app.appendChild(space);
            }
          });
        }
        else {
          const errorMessage = document.createElement('marquee');
          errorMessage.textContent = "Request failed.";
          app.appendChild(errorMessage);
        }
      }
      request.send();
    }; 
```

```
 PageModule.prototype.loadImages = function(inputGenre) { // our function now takes in a "genre" input
      const app = document.getElementById('leftColumn');

      var request = new XMLHttpRequest();
      request.open('GET', 'https://asset-bdf37.firebaseio.com/results.json', true);

      request.onload = function () {
        // Begin accessing JSON data here
        var data = JSON.parse(this.response);
        if (request.status >= 200 && request.status < 400) {
          Object.keys(data).forEach(result => {
            if(data[result].genre == inputGenre){ // we'll only want to display images for a specific genre
              const bookCovers = document.createElement('img');
              bookCovers.src = data[result].image_url;
              console.log(result);
              app.appendChild(bookCovers);
              const p = document.createElement('p');
              p.textContent = "\n";
              app.appendChild(p);
            }
          });
        }
        else {
          const errorMessage = document.createElement('marquee');
          errorMessage.textContent = "Request failed.";
          app.appendChild(errorMessage);
        }
      }

      request.send();
    };
```

The code should look like:<br>

![](/images/lab300/300-david-search-7.png)<br>

<b> Call Search Function </b>

Now that we have our logic, let's bind this logic to an action. Under `Designer` view, click the `Search` button. Under the `Events` tab, click `New Event -> Quick Start Click`. 

![](/images/lab300/300-david-search-8.png)<br>

An action chain window has popped up. Drag over a `Call Module Function`. Click `Select Module Function`. Under "Page Functions", select our `loadImages` function.<br>

![](/images/lab300/300-david-search-9.png)<br>

Recall that our function now takes in a paramter, so on the right side under `Input Paramters`, map `inputGenre` to our `Genre` variable. Click `save`.<br> 

![](/images/lab300/300-david-search-10.png)<br>

Now perform the same steps for the `loadDescriptions` function (drag another module function in for the loadDescriptions function, and bind the paramters to the function). The end action chain should look like this: <br>

![](/images/lab300/300-david-search-11.png)<br>

Let's test our page out. Click the `Live` button at the top right corner. Enter in `Fantasy` and hit search. Our website now loads all the books with the fantasy genre! <i>(If the search button displays at the bottom of the page instead of the top, re-order the left-column and right-column HTML divs to the end of your page HTML code).</i>

![](/images/lab300/300-3-ds12.png)<br>

Try hitting the search button again. Uh oh, looks like the page is getting populated with the same books every time someone hits search. 

![](/images/lab300/300-david-search-13.png)<br>

We'll fix this by first removing the book images/descriptions every time someone hits search before loading the new images/descriptions.<br>

Go to the `js` tab, and paste in the following function that will clear the book images/descriptions:

```
PageModule.prototype.resetPage = function () {
    const col1 = document.getElementById('leftColumn');
    const col2 = document.getElementById('rightColumn');
    while (col1.firstChild) { // while there are images, remove them
      col1.removeChild(col1.firstChild);
    }
    while (col2.firstChild) { // while there are descriptions, remove them
      col2.removeChild(col2.firstChild);
    }
  };
```

With the `resetPage` function added, the code now should look like:<br>

![](/images/lab300/300-david-search-14.png)<br>

With this new function added, navigate to our action chain that invokes the `loadImage` and `loadDescription` functions. Add a new `module function` that calls on the resetPage function. 

![](/images/lab300/300-david-search-15.png)<br>

![](/images/lab300/300-david-search-16.png)<br>

The final action chain should look like so:<br>

![](/images/lab300/300-david-search-21.png)<br>

Now go back to the `Designer` view, click the submit button, and bind this action chain to whenever someone clicks the search button. There are now three actions within this action chain. One to remove any previous search results, one to load descriptions, and the last to load images.

![](/images/lab300/300-david-search-17.png)<br>

Try loading the page again. It works! We have now successfully implemented the search functionality.
