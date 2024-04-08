---
# Push to Production with Heroku
---

Time to ship some code! Since we've built and styled our Rotten Potatoes app, let's show it to the world by putting it online. 

# Render

So Heroku started to charge (read instructions below) so we are going to use a service called Render and MongoDB Atlas to go live.

## MongoDB Atlas

1. Go to [MonogoDB Atlas](https://www.mongodb.com/) and create an account if you haven't already
2. Create a new free-tier cluster
3. Create a user and keep the user's password for later.
4. Create a new database. Click on "connect" and then "drivers" and copy the connection string.
5. Replace `<password>` in the connection string with the database's user's password.

## Render steps

1. Go to [render.com](https://www.render.com) and create an account if you haven't already.
2. Create a new web service named the same as your project.
3. Set an environment variable called `MONGODB_URI` and set it equal to the connection string you got from your MongoDB Atlas database.
4. Now in your `app.js` make sure your `mongoose.connect(process.env.MONGODB_URI || <localhost mongodb path>)` command looks like this.
5. Be sure to wrap your `require("dotenv").config()` in `app.js` in a conditional block so it only is called in development. Like this:

```js
if (!process.env.PORT) {
  require("dotenv").config()
}
```
6. Now push your code to render and check that the app loaded properly and connected to the production MongoDB Atlas database.

# Heroku

We'll use a service called [Heroku](https://www.heroku.com) that is free, but it does require a **credit card** to be on file.

# Sign Up For and Install Heroku

> First you need to create a Heroku account at [heroku.com](https://www.heroku.com).

> Then you will need to add the Heroku Command Line Interface (CLI) to your bash terminal so we can interact with the Heroku service via the terminal and git. Follow [these instructions](https://devcenter.heroku.com/articles/heroku-cli) to install the Heroku CLI.

# Adding Your Procfile

Before we push to Heroku, we'll have to create a special file, called `Procfile`, that lets Heroku know how to run your website.

> Create and open your new `Procfile` using the following commands:

```bash
$ touch Procfile
$ atom Procfile
```

Next, we need to insert the command to run our application on Heroku.

> Paste the following into your new `Procfile` and save:

```bash
web: node app.js
```

Great! When we push to Heroku, it'll know how to run our application. Keep going!


# Pushing to Heroku using Github

Let's start by committing what we have so far so that we have that `Procfile`

> Commit the `Procfile`

```bash
$ git add .
$ git commit -m 'adding Procfile'
```

Now let's use the `heroku` command to create a heroku app and name it with "rotten-potatoes" and then our initials. So someone named Samantha Bee would be "rotten-potatoes-sb". This `heroku create` command will add our heroku app as a git remote repository that we will be able to push to using the git command `git push`. We can see our remote repos by using the command `git remote -v`.

> Create your heroku app, replacing `MY-INITIALS` with your initials

```bash
$ heroku create rotten-potatoes-MY-INITIALS
$ git remote -v
```

Alright, now we can push our code to heroku and open our new website!

> Push to Heroku!

```bash
$ git push heroku main
$ heroku open
```

You are probably seeing an error screen right now! Bit of a let down maybe. But also a good lesson - **Things never work the first time**. Let's debug our issues.

# Scale Your Heroku application

We need to run an additional command that tells Heroku to assign a free worker to our deployment in order to run your website. In Heroku syntax, that means executing the `ps:scale` command, like so:

> Run the `ps:scale` command:

```bash
$ heroku ps:scale web=1
```

**Just like `heroku create`, you only need to execute this command once per project**!

# Heroku Logs

When ever you have an error or problem with Heroku, you can see the logs by running `heroku logs`.

```bash
$ heroku logs
```

If you want to see the logs to continually you can add `--tail` to this command.

```bash
$ heroku logs --tail
```

What error are we seeing in heroku now? What do we need to do?

> You might need to add a "start" command to your `package.json` file:

```json

"scripts": {
  "start": "node app.js"
}
```

# Adding a Production Database

It looks like the error is that we cannot connect to our mongodb database. That's because it is looking at the `'mongodb://localhost/rotten-potatoes'` URI, but that is on our local computer and heroku, which is remote, doesn't have access to that. So we have to add a mongodb heroku add-on; until recently we used an add-on called [mLabs], but that has been replaced by MongoDB ATLAS. It's a little bit more involved than mlabs.

Here are some good clear directions how to set up your Atlas Cluster in 5 steps, followed by a short tutorial showing how to connect your Atlas cluster to your Heroku app. The tutorial has you clone some code, so I encourage you to open a seperate code editor and follow the instructions using the code provided. Then come back to your rotten-potatoes project and repeat the instructions.

> https://www.mongodb.com/developer/products/atlas/use-atlas-on-heroku/#prerequisites


Our next step is to point to this production mongodb database URI in our `app.js` file.

> Update `app.js` to point to the mongodb URI if it exists:

```js
// app.js

var mongoose = require('mongoose');
mongoose.connect(process.env.MONGODB_URI || 'mongodb://localhost/rotten-potatoes', { useNewUrlParser: true });
```

Now if we try to open our heroku app via the terminal using `heroku open`, what happens?

# ... Hanging?

Another error! This is a weird one. First it just hangs for a while, then times out, and then the error says "cannot bind on $PORT". This is because Heroku does not use port 3000, it uses another port available in production at `process.env.PORT`, just like your mongoDB URI.

> Let's fix that by setting the port also with the `process.env`. Then we have to point to this production mongodb database URI in our `app.js` file.

```js
// app.js

const port = process.env.PORT || 3000;
app.listen(port);
```

Now if we try to open our heroku app what happens?

# WooHoo!

Awwww yeah - you did it! You made your first RESTful and Resourceful app using Node.js, Express.js, and MongoDB!
