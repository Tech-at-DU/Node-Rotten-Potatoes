---
# See All Reviews
---

Following our **User Stories** we are going to define a single **Resource** in this app called a `Review` so that we have something to CRUD:

1. Users can view all reviews (index)
1. Users can create a review (new/create)
1. Users can view one review (show)
1. Users can delete a review (destroy)
1. Users can edit a review (edit/update)
1. Users can comment on reviews (comments#create)
1. Users can delete comments on reviews (comments#destroy)

A **Resource** is an abstract object that we use to organize data, code, and the features of our app. For example, a `User` resource we can keep track of logging in and out, email and passwords, and people's birthdays. In a blog, we might have an `Article` or `Post` resource where we would track the titles and bodies of articles and keep track of the code for publishing and sharing them.

Resources can also be related to each other. For example, articles may have comments; a building resource might have floors, and the floors may have units; a user might have friends (like in Facebook).

All resources have a few actions in common that are called **Resourceful Routes**.

> Review this table of routes, as we'll be referencing it frequently in this tutorial, and you should be familiar with what each route does:

| URL              | HTTP Verb | Action  | What it Does |
|------------------|-----------|---------|---------------|
| /reviews          | GET       | index   | See all reviews |
| /reviews/new      | GET       | new     | See new review form |
| /reviews          | POST      | create  | Create a new review |
| /reviews/:id      | GET       | show    | See one review |
| /reviews/:id/edit | GET       | edit    | See an edit review form |
| /reviews/:id      | PATCH/PUT | update  | Update a review |
| /reviews/:id      | DELETE    | destroy | Delete a review |

The table above shows the resources, routes, and actions that might be used on a photo sharing site like Instagram.

We're going to start with the index action and then show, and then we'll look at the create, edit, update, and delete.

# Adding a /reviews Route

Let's make a route to `/reviews` for the index action where we can see all the reviews that we've created. Eventually this will be on our root route, but we can start making it its own separate path.

> Add the following to `app.js`:

```js
// app.js

// OUR MOCK ARRAY OF PROJECTS
let reviews = [
  { title: "Great Review", movieTitle: "Batman II" },
  { title: "Awesome Movie", movieTitle: "Titanic" }
]

// INDEX
app.get('/reviews', (req, res) => {
  res.render('reviews-index', { reviews: reviews });
})
```

Notice how we are making a mock array of reviews, and sending that in as an object into the template `{ reviews: reviews }`. So the variable `reviews` will be available in our template.

# Errors are Your Friends!

If you refresh `localhost:3000/reviews` right now what do you see? An error! **That's ok!** Errors are our friends. What does the error say? I bet it says something like "I can't find the template 'reviews-index'". That makes sense because we haven't made it yet! It's ok to complete coding tasks in an order that throws a predictable error.

> Errors often tell you the next step you need to take. In this case the error is telling you that the template does not exist. Let's make it!

So to fix this error, let's add the template `views/reviews-index.handlebars`. We're going to use the Handlebars.js `{{#each}}` iterator to loop over our array of reviews and display each one's title.

> Create the `views/reviews-index.handlebars` template and add the following to it:

```html
<!-- reviews-index -->
<h1>Reviews</h1>
{{#each reviews}}
  <h2>{{this.title}}</h2>
  <small>{{this.movieTitle}}</small>
{{/each}}
```

If you refresh `localhost:3000/reviews` now what do you see?

> You should see the mock reviews we wrote into code. Can you add to them or change them?

# Setting the Root Route - '/'

> Let's update the `/reviews` route to be our root route. Just change the path from `/reviews` to `/` and delete or comment out the hello world root route we made before.

```js
// app.js

// INDEX
app.get('/', (req, res) => {
  res.render('reviews-index', { reviews: reviews });
})
```

Now navigate to `localhost:3000`. Do you see the mock reviews displayed?

# Now Commit

```bash
$ git add .
$ git commit -m 'Users can see all reviews'
$ git push
```

# Stretch Challenge

Throughout Make School tutorials, you'll see sections designated by a green box with a trophy symbol. These are **stretch challenges**, and are provided for those who want to dive deeper on a topic, or get more practice. They are not required to complete the tutorial, but we encourage you to try them! More practice is always beneficial!

> Can you make changes to your `reviews` array in `app.js` and see it reflected in your `reviews-index` template?
