---
title: "See All Projects"
slug: index-projects
---

We are going to define a single **resource** in this app called a `Project`.

A resource is an abstract object that we use to organize data, code, and the features of our app. For example, a `User` resource we can keep track of logging in and out, email and passwords, and people's birthdays. In a blog, we might have an `Article` resource where we would track the titles and bodies of articles and keep track of the code for publishing and sharing them.

Resources can also be related to each other. For example, articles may have comments; a building resource might have floors, and the floors may have units; a user might have friends (like in Facebook).

All resources have a few actions in common that are called RESTful routes. If there was anything worth memorizing in this tutorial, this is it:

![Restful routes](assets/RESTful-routes.png)

We're going to start with the index action and then show, and then we'll look at the create, edit, update, and delete.

# Adding a `/projects` Route

Let's make a route to `/projects` for the index action where we can see all the projects that we've created. Eventually this will be on our root route, but we can start making it its own separate path.

```js
// app.js

// OUR MOCK ARRAY OF PROJECTS
var projects = [
  { title: "Great Project" },
  { title: "Next Project" }
]

// INDEX
app.get('/projects', function (req, res) {
  res.render('projects-index', {projects: projects});
})
```

Notice how we are making a mock array of projects, and sending that in as an object into the template `{projects: projects}`. So the variable `projects` will be available in our template.

# Errors are Your Friends!

If you refresh `localhost:3000` right now what do you see? Probably an error. That's ok! Errors are our friends. What does the error say?

> [info]
> Errors often tell you the next step you need to talk. In this case the error is telling you that the template does not exist. Let's make it!

Now let's add the template `views/projects-index.handlebars`. We're going to use the Handlebars.js `{{#each}}` iterator to loop over our array of projects and display each one's title.

```html
<h1>Projects</h1>
{{#each projects}}
  <h3>{{this.title}}</h3>
{{/each}}
```

If you refresh `localhost:3000/projects` now what do you see?

# Setting the Root Route - `/`

Let's update the `/projects` route to be our root route. Just change the path from `/projects` to `/` and delete or comment out the hello world root route we made before.

```js
// app.js

// INDEX
app.get('/', function (req, res) {
  res.render('projects-index', {projects: projects});
})
```

Now navigate to `localhost:3000`.

# Extra Challenge

Can you make changes to your `projects` array in `app.js` and see it reflected in your `projects-index` template?