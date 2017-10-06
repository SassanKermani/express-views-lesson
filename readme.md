![](https://ga-dash.s3.amazonaws.com/production/assets/logo-9f88ae6c9c3871690e33280fcf557f33.png)
# Views in Express

<!--Didn't get started till 10 in WDI2...goddamit so many questions on EJS. -->

<!--WDI5 9:45 -->
<!--9:51 WDI4-->
<!--9:35 WDI3-->
<!--9:35 5 minutes -->

<!-- Raise your hand if you're tired of displaying JSON day in and day out.  Today, we're going to give some more power to our back-end.  Today, we will introduce server-side rendering, creating pages with the full support of all three amigos: HTML, CSS, and JS. -->

### Objectives
*After this lesson, students will be able to:*

- **Create** and **render** views in an Express application
- **Implement** and **use** a variety of Express middleware
- **Explain** the differences between views and partials in EJS
- **Render** partials and iterate over data in views

### Preparation
*Before this lesson, students should already be able to:*

- **Write** express controllers and models
- **Connect** Express models to Mongoose/MongoDB

## Views in Express - Intro

A lot of times, if we're looking to get a prototype up and running, we'll want to use a templating engine. Using Express out of the box, the view engine is Jade - an engine very similar to haml that relies on white space.  But if you're not familiar with haml - as we're not - EJS is another alternative that's easy to set up and does not rely on white space.

We are going to return to our `candies` app to focus on how to add views using EJS, include partials and pass data to those views.

<!--9:40 10 minutes -->

## Setting up our app to use EJS - Catch-up

First, let's require `ejs`. In our application's `package.json`, inside dependencies, add:

```json
"ejs": "^2.4.2",
```

where you see

`// require ejs 2.4.2 //`

...and then run:

`npm install`

Next, we will need to set up our database.  Run `node db/seed.js` and you should see that the candies were created in Terminal.

<!--9:58 WDI4 turning over to devs -->

Now, let's take a look at our `app.js` file and add the following:

```javascript
app.set('views', path.join(__dirname, 'views'));
app.engine('ejs', require('ejs').renderFile);
app.set('view engine', 'ejs');
```

<!--WDI5 10:03 -->
<!--WDI3 9:46 when turning over to devs -->

Let's look at a few things going on here: `path` is a core Node module dealing with paths.  In our example, we've used the [path.join()](https://nodejs.org/api/path.html#path_path_join_path1_path2) method, which combines the directory that `app.js` is in (`app`) with `views` to tell our node app to look for views inside `app/views`.

The middle line requires the `ejs` module in our app for files it encounters with the `.ejs` file extension. More specifically, it requires the `renderFile` method we used in our [EJS Intro](https://github.com/den-materials/intro-ejs).

The second `app.set()` tells Express to use the ejs templating engine. This allows you to embed JavaScript to work with data (especially with conditionals and loops) in your views.  For example, we can choose not to render the login section of our page if a user is already logged in. The file path to your view files now will end in `.ejs`

Since we're ready to use `.ejs` now, let's set up our file structure to make sure our application can call the files properly. Create the following folder structure inside the `app` folder:

```
- views/
----- partials/
-------- candy/
------------ form.ejs
----- layout.ejs

```

<!--10:07 WDI4 turning over to devs -->
<!--10:13 WDI4 coming back -->
<!--9:53 starting to explain WDI3 -->
<!--9:56 WDI3 when turning over to devs -->
<!--9:50 10 minutes -->

## Set up your form - Independent Practice

Ok, you've done this before.  Set up your form real quick in `candy/form.ejs` with:

- A heading that says "Create Candy!"
- A form with a POST method that submits to the `/candies` endpoint
- Two inputs for name and color that have placeholders
- A submit button

<!--10:16 WDI4 turning over to devs -->

Note that HTML is always valid EJS, so you can do this entirely in HTML.

<!--
> Note: Provide students with the correct answer a couple minutes before time up

```html
<h3>Create Candy!</h3>
<form method="POST" action="/candies">
  <input name="name" placeholder="Name"/>
  <input name="color" placeholder="Color"/>
  <input type="submit" value="Submit">
</form>
```
-->

<!--10:25 WDI2-->
<!--10:22 WDI4 -->
<!--WDI5 10:13  -->
<!--10:04, 10:13 turning over to devs WDI3-->
<!--10:00 10 minutes -->
<!--half-mast -->

## Set up our layout to iterate over data - Catch-up

Let's create an index page that will double as our form.  But first, let's make sure our application is set up to pass data from our database to our views if a user visits the `/candies` endpoint.

First, in our `config/routes.js` file, let's make sure our app can both get a list of all the candies and post a new candy from the same endpoint:

```javascript
// require the controller
var candiesController = require('../controllers/candies');

// http://127.0.0.1:3000/candies
router.route('/candies')

//GET all candies
   .get(candiesController.getAll)

//POST a new blob
   .post(candiesController.createCandy);
```

<!--10:26 WDI4 turning over to devs -->
<!--WDI5 10:21  -->

First, we require the controllers, then we set the candies routes and the `GET` AND `POST` actions that will occur when hitting this endpoint.  Now, let's define these methods in our controller: `.getAll` and `.createCandy`.  We'll need to set up error handling and serve the response to the correct view with `.render`.  First, let's start with the `.getAll` function, together:

```javascript
// GET
function getAll(request, response) {
  Candy.find(function(error, candies) {
    if(error) response.json({message: 'Could not find any candy'});
    console.log("Rendering candies");
    response.render('layout', {candies: candies});
  });
}
```

<!-- End half-mast -->

<!--10:21 WDI3 -->
<!--10:34 WDI4 turning over to devs -->
<!--10:10 5 minutes -->

## Write a `createCandy` method that will pull from `name` and `color` inputs - Independent Practice

<!--Ask students how they will build candy (using request.body), use mongoose intro as a reference -->

```javascript
// POST
function createCandy(request, response) {

  // fill in your code here

  candy.save(function(error) {
    if(error) response.json({messsage: 'Could not create candy b/c:' + error});

    response.redirect('/candies');
  });  
}
```

<!--
> Note: Provide students with the correct answer once time is up

    ```
    console.log('in POST');
    console.log('body:',request.body);
    var candy = new Candy();

    candy.name = request.body.name;
    candy.color = request.body.color;
    ```
-->

<!--10:38 WDI5 -->
<!--10:15 15 minutes -->

<!-- Start server and go to http://localhost:3000/candies --we won't see anything but there will be a note in the Terminal. -->

## Let's make our layout! Code along

First, all the header stuff is exactly the same as it would be in a `.html` file - in `layout.ejs`:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Candy App</title>
    <link rel="stylesheet" type="text/css" href="stylesheets/main.css" />
</head>
```

<!--Show what localhost:3000/candies looks like at this point -->

Even though this is an `.ejs` file we're able to write html because we've set up our app to use the ejs templating engine, which can render HTML **and** embedded JavaScript.

Now, we're going to do two things.  First, because we're pulling `candies` from our database, we'll have access to all of our `candies` and their associated attributes.  So let's iterate over that array with JavaScript.  Just like earlier, we specify that what's being read is JavaScript with opening and closing `<% %>`, if we want the code to execute; with `<%= %>` if we want the code to execute **and render** on the browser. 

> Note: Recent versions of EJS have [a few more options](https://www.npmjs.com/package/ejs#tags)

Now let's create a `<body>` and show all of our candies, with their name and color:

```html
<body>
  <h1>
    <a target="_blank" href="https://www.youtube.com/watch?v=mKli0y-Xr-Q">Candy Shoppe</a>
  </h1>
  <div class="container">
  <% for(var i=0; i< candies.length; i++) {  %>
    <ul>
      <li><b>Name : </b> <%= candies[i].name %></li>
      <li><b>Color : </b> <%= candies[i].color %></li>
    </ul>
  <% } %>
  </div>
</body>
```

<!--11:17 actually WDI2-->
<!--10:48 turning over to devs WDI4 -->
<!--WDI5 coming back 10:52 -->
<!--WDI4 coming back 10:59-->

Now we can use partials within our `layout.ejs` page.  The method we use is `include`:

```html
...
    <hr>
    <div class="container">
        <% include ./partials/candy/form %>
    </div>
...
```

Now your `layout.ejs` page should look like this:

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Candy App</title>
    <link rel="stylesheet" type="text/css" href="stylesheets/main.css" /></head>
<body>
    <h1>
        <a target="_blank" href="https://www.youtube.com/watch?v=mKli0y-Xr-Q">Candy Shoppe</a>
    </h1>
    <div class="container">
    <% for(var i=0; i< candies.length; i++) {  %>
    <ul>
      <li><b>Name : </b> <%= candies[i].name %></li>
      <li><b>Color : </b> <%= candies[i].color %></li>
    </ul>
    <% } %>
    </div>

    <hr>
    <div class="container">
        <% include ./partials/candy/form %>
    </div>

</body>
</html>
```

<!--Go to candy shoppe link, then try to "Submit", see what happens-->

<!--WDI5 10:57, helped unstick devs then took break  -->
<!--WDI3 10:54 after giving directions -->
<!--WDI4 11:15 after giving directions -->
<!--10:30 15 minutes -->

## Independent Practice

<!--WDI5 11:10 -->

> ***Note:*** This will be a pair programming activity

Expand on this application by doing the following:

- Create a footer as a separate partial and render it in the layout.ejs file, the same way we added the form partial
  - In the footer add in "Candies ©"
- Add a link to an update page to each candy on your `/candies` page, with the `link_to` helper\*
  - This link should go to `edit/<candy_id>`, and should say "Update"
  - If your link is appearing a `<a href...` instead of a blue hyperlink, try using `<%-` instead of `<%=`
- Create an edit page where users can edit information about their candy
  - You will need to create a route for this first
  - You can re-use your candy form file for the page, however, make sure you use the "PATCH" method instead of "POST"
  - The update functionality is written for you already!
  
\* Use these [docs](http://www.embeddedjs.com/getting_started.html).

<!--WDI4 11:25 -->
<!--WDI5 11:40 -->
<!--10:45 5 minutes -->

## Conclusion (5 mins)
- Describe the difference between `ejs` views and partials.
- Describe how to configure your Express app to use `ejs`.
- Explain how `ejs` lets us render dynamic data on a particular view.

<!--WDI3 finished 11:27 -->
<!--WDI5 11:53 after going over Q'ns and quickly showing soln code -->

#### Common Middleware We Might Use:
* [EJS](https://www.npmjs.com/package/ejs)
* [body-parser](https://www.npmjs.com/package/body-parser)
* [method-override](https://www.npmjs.com/package/method-override)
* [lazy-cache](https://www.npmjs.com/package/lazy-cache)
* [express-helpers](https://www.npmjs.com/package/express-helpers)
* [path](https://docs.nodejitsu.com/articles/file-system/how-to-use-the-path-module/)
* [morgan](https://www.npmjs.com/package/morgan)
* [cookie-parser](https://www.npmjs.com/package/cookie-parser) 

## Licensing
All content is licensed under a CC­BY­NC­SA 4.0 license.
All software code is licensed under GNU GPLv3. For commercial use or alternative licensing, please contact legal@ga.co.
