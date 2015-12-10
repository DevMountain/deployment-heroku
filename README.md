Deploying a Node.js app with Heroku
=======================

These steps assume that you've already created a Heroku account. You also need to install the [Heroku Toolbelt](https://toolbelt.heroku.com/)

To ensure that everything is set up correctly, you can type in `heroku` in your terminal and you should see some help text that shows up on the screen.

##Log in with Heroku

```
heroku login
```

Enter your email and password, and this will kick off the process that will create a Heroku SSH key for your machine and register it with Heroku. **Make sure you select Y when asked to upload your key to Heroku.**

##Create the remote

```
heroku create
```

This will create an app on Heroku as well as add a new git remote to your repo. **Note: if you created an app already directly on the Heroku website, you'll have to manually add the Heroku remote (e.g. `heroku git:remote -a murmuring-sea-7101`).**

You can rename the default app name given to you by Heroku (something like calm-mountain-5235) by typing:

```
heroku apps:rename my-cool-app-name --app calm-mountain-5235
```

##Deploying Your App

```
git push heroku master
```

Here's the cool part. The above push will automatically trigger a Heroku build process that will create a dyno (a virtualized server) that will install all the necessary dependencies (think package.json).

Now let's see your app in action. Type:

```
heroku open
```

This should spawn a browser window pointed to your dyno instance. If you're seeing errors, we may be missing dependencies, like MongoDB. Let's get that worked out.

##Installing MongoDB on Heroku
Heroku doesn't give you access to the actual OS or isntance of your Dyno. This means we can't install MongoDB. To do this we have to use a 3rd party add-on called MongoLab. MongoLab is a MongoDB-as-a-Service that lets us connect our Dyno to a separate Mongo instance. This is a paid service, but they provide a free sandbox you can use.

Let's install the mongolab add-on (note that you'll likely have to have a credit card on file with Heroku to do this):

```
heroku addons:create mongolab
```

This will initialize a mongolab instance that you can connect to.

We need to refactor our API to connect to this instance of Mongo on a separate server. We'll use environment variables to do this. If you need to review what environment variables are and why we use them, see [this part of the digital ocean guide](https://github.com/DevMountain/deployment-digitalocean#environment-variables).

Your connection code in Node will end up looking something like this:

```
// Here we find an appropriate database to connect to, defaulting to
// localhost if we don't find one.
var uristring =
process.env.MONGOLAB_URI || 'mongodb://localhost/my-app';

mongoose.connect(uristring);
```

Note how we rely on the MONGOLAB_URI environment variable (that will be specified by Heroku) and that we'll fall back to the localhost url for our local development.

Also, you'll want to make sure that you're relying on an environment variable to have express/Node connect on. Something like this:

```
app.listen(process.env.PORT || 8080);
```

Heroku automatically sets the PORT variable, so just as before, we can rely on Heroku to set the variable or it will fall back to our default value in our local environment.

That should get you up and going with Node/MongoDB and Heroku!
