---
# Adding a Route for Comments
---

Now it's time to add a route to handle comments.

# Creating Comments

You need to define a route. Here is the list of the current routes with a new one added for the comment form at the bottom.

| URL              | HTTP Verb | Action  |
|------------------|-----------|---------|
| /                | GET       | index   |
| /reviews/new     | GET       | new     |
| /reviews         | POST      | create  |
| /reviews/:id     | GET       | show    |
| /reviews/:id/edit| GET       | edit    |
| /reviews/:id     | PUT/PATCH | update  |
| /reviews/:id     | DELETE    | Destroy |
| /reviews/comments | POST      | create  |

The Comment route uses the POST method since creating a Comment will be sending data and creating new resources.

# Setting the Action on the Comment Form

Get the form to call the new route by adding an action. Make sure to set the method as `post`, use the route defined above.

> Set the method to `post` in the commend form on `views/partials/comment-form.handlebars`:

```HTML
<!-- views/partials/comment-form.handlebars -->

<form action="/reviews/comments" method="post">
  <fieldset>
    ...
```

# Associating Comments with Reviews through Review Id

Comments belong to a single review, so we need to keep track of this relationship. We need to **Associate** a comment with a review. The comment will be a **child** to the **parent** review. This relationship can be expressed through these two phrases:

1. Reviews have many comments
1. Comments belong to a review

We'll associate a comment with a review by attaching a `reviewId` attribute to each comment that points to its parent review id. We have to set this at the time of creation, so we can do this by adding a hidden input field that sends in the parent review id.

# Add a Hidden Field

Let's include the `_id` of the review with the form. One way you can accomplish this is using a **hidden form field**. A hidden field is an input tag that has a value that isn't visible in the browser.

> Add a hidden field to the form and set the value to the id of the review in `views/partials/comment-form.handlebars`:

```HTML
<!-- views/partials/comment-form.handlebars -->

<form action="/reviews/comments" method="post">
  <input type="hidden" value="{{review._id}}" name="reviewId">
  <fieldset>
    ...
```

The hidden field get's a value from the `review._id` and has a name of `reviewId`.

Submitting this form will include the id of the review along with the title and content input by the user writing the comment.

# Define a Route  

Define a new route in express to handle this new form. We can do this inside of a new `comments.js` controller.

> Create a new file `controllers/comments.js`.

> Once that's done, add this to `controllers/comments.js`:

```js
// comments.js

module.exports = (app, Comment) => {

  // NEW Comment
  app.post('/reviews/comments', (req, res) => {
    res.send('reviews comment')
  })

}
```

This should print the message 'reviews comment' to the browser when the form is submitted. This is a good test to check if everything is working so far.

# Comment Model

To save comments to the database you need a Comment model. You'll need to define a new model for Comments.

> Make a new file in your `models` folder called `comment.js`: `models/comment.js`

```js
// comment.js

// imports `mongoose`. We need `mongoose` here to create the new model.
const mongoose = require('mongoose')

//similar to the Review model with the difference that the Comment model only saves a `title` and `content`.
const Comment = mongoose.model('Comment', {
  title: String,
  content: String
});

// exports the `Comment` object. By doing this you can import `Comment` into any of your other files.
module.exports = Comment
```

Lastly, require `Comment` into `app.js`, right underneath your require for `Reviews`:

> Add the require in `app.js`:

```js
// app.js

const reviews = require('./controllers/reviews')(app);
const comments = require('./controllers/comments')(app);
```

> Also add the following underneath the other model require in `review.js`, we'll need it later to actually show our comments.

```js
//review.js

...
const Comment = require('../models/comment')
...
```

# Adding a reference to a Review inside a Comment

Every Comment will need to have reference to a Review. This reference is the `_id` of the Review. Remember, id's are unique so every Review can ask for all Comments that reference it's id. Or conversely, any Comment will be able to refer to the Review the Comment was written for through the the review id it owns.

> Open `models/comment.js` make the following changes:

```js
const mongoose = require('mongoose')
const Schema = mongoose.Schema

const Comment = mongoose.model('Comment', {
  title: String,
  content: String,
  reviewId: { type: Schema.Types.ObjectId, ref: 'Review' }
});

module.exports = Comment
```

On the second line you got a reference to the Mongoose Schema object. This Object helps Mongoose organize all of the models that it is using.

The second change was to add a new property: `reviewId`. The value of this property is a `ref` or reference to another Mongoose model, 'Review' in this case. This name must match the name given to the model in `models/review.js`:

```js
...
const Review = mongoose.model('Review', {
  ...
})
```

You can check your work by testing your project in the browser. Any errors should show in the terminal. Comments cannot be saved yet but, saving Reviews should still work.

Submitting a comment the browser should show the message: 'reviews comment' from `res.send('reviews comment')` from the post comment route in `app.js`.

> Revisit the post comment route now. Open `controllers/comments.js` and look for `app.post('/reviews/comments', ...)`. You need to create a new comment and then reload the page. Make the changes below:

```js
// comments.js

const Comment = require('../models/comment');

module.exports = (app) => {

    // CREATE Comment
    app.post('/reviews/comments', (req, res) => {
      Comment.create(req.body).then((comment) => {
        console.log(comment)
        res.redirect(`/reviews/${comment.reviewId}`);
      }).catch((err) => {
        console.log(err.message);
      });
    });
}
```

Note the addition of requiring the `comment` model.

Test your work. Write a comment and submit the form. The `console.log` call will allow you to see your comment in the console. If there are any errors they should also appear in the console.

# Loading Comments for a Review

You still can't see comments in the browser! Let's tackle that problem next.

This is a chicken and egg problem. We'd like to see comments but can't see any if they don't exist in the database. For this reason we chose to write the code that create comments first. Now we will write the code to display comments.

> Open `controllers/reviews.js` and make these changes to the route that shows a single review by id. Make sure to also require the `Comment` model in this controller, since we'll be using it now too!

```js
//reviews.js
const Review = require('../models/review');
const Comment = require('../models/comment')

module.exports = function(app) {
...

  // SHOW
  app.get('/reviews/:id', (req, res) => {
    // find review
    Review.findById(req.params.id).then(review => {
      // fetch its comments
      Comment.find({ reviewId: req.params.id }).then(comments => {
        // respond with the template with both values
        res.render('reviews-show', { review: review, comments: comments })
      })
    }).catch((err) => {
      // catch errors
      console.log(err.message)
    });
  });

...

}
```

This is an interesting use of `Promise`! `Promise.all()` runs any number of asynchronous requests in parallel (at the same time). The code in the `then()` block is run when all of the requests have resolved. Values from the requests are returned in an array, `values` in the example above. Where `values[0]` is the first request, `values[1]` is the second request etc.

In the code block above there are two requests `[findReviews, findComments]` when these resolve `values[0]` will be an array if reviews, and `values[1]` will be an array of comments.

# Displaying comments

Now that you have an array of comments we need to display them.

A partial will be a good way to keep your code clean and organized.


> Create a new file `views/partials/comment.handlebars`.

```HTML
<!-- views/partials/comment.handlebars -->

<div class="card">
  <div class="card-block">
    <h4 class="card-title">{{this.title}}</h4>
    <p class="card-text">{{this.content}}</p>
  </div>
</div>
```

> Now display comments using this partial in `views/reviews-show.handlebars`.

```HTML
<!-- views/reviews-show.handlebars -->

<a href="/">Back to Home</a>

    ...

    {{> comment-form}}

    <hr>

    <!-- Show Comments -->
    {{#each comments}}
      {{> comment}}
    {{/each}}

  </div>
</div>
```

Use an `{{#each ...}}` block to display one copy of the `comment.handlebars` partial for **each** item in the comments array.


> Remember the `>` in `{{> comment}}` signifies that `comment.handlebars` is a partial!

Test your work! Navigate to a single Review and add a comment. Submitting the comment should display a new comment below the Comment form.

# Now Commit

```bash
$ git add .
$ git commit -m 'Users can see comments'
$ git push
```

# What Just Happened?

You created a relationship between two different document/records in your database. Reviews each have a unique id. By saving the id of a Review with a Comment comments can find the Review that they are are associated with. Reviews can also find all of the Comments that are associated with their id.

This is a **one to many relationship**. This an important concept in database design, and an important tool you will use when managing data.
