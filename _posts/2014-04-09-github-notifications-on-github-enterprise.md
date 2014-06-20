---
layout: post
title: GitHub Notifications on GitHub Enterprise
---

The great [Brandon Keepers](http://opensoul.org/) has come up with a really sweet interface for keeping track of notifications on GitHub. It's especially handy if you watch a whole bunch of repositories; lumping all of the comments into a big list and showing them in an email-style pane system. Definitely take some time to [see it in action](https://notifications.githubapp.com/) and check out the [code repo](https://github.com/bkeepers/github-notifications) that makes it hum.

This is all well and good, but what if you want to get this up and running on your GitHub Enterprise system? The point of most GHE installations is to be more secure by hosting the system inside a company's firewall. This means that traditional cloud hosts like Heroku won't have access to your internal GHE instance. However, the project is set up so that you can set it up on your own network and configure it to work your own GHE instance.

Hopefully you have access to some kind of virtualization system within your network that allows you to create a cheap and low-powered virtualized server. Just about anything will host this project. (I'm using a 1GB Ubuntu 12.04 instance on our internal OpenStack system.) The server actually does very little, as the code directs the browser to be responsible for hitting the API directly to get the data.

## Register the application on GitHub

Since this project relies on the use of GitHub's API, we will need to register the application in the GitHub Enterprise system. This isn't terribly hard, but you'll need to switch gears from terminal to web browser. Go through the following steps:

1. Log into your internal GitHub Enterprise instance
2. In the upper-right corner of the window, click the "Accout Settings" icon
3. On the left side bar, select the user or organization you want to register the application as
4. Click into the "Applications" section
5. Click "Register new application"
6. Fill out the for to register the new application
  * Application name: GitHub Notifications
  * Homepage URL: http://[server-ip]:8000/
  * Callback URL: http://[server-ip]:8000/
7. Click "Regiser application" button

Nice! Now your application has been registered on GitHub Enterprise. Keep this page open, since you'll need it for the Client ID and Client Secret values later.

## Set up the GitHub Notifications project

It's now time to clone the project repository. Terminal into the server and get into whichever directory you'd like to clone the repo into. Remember that git automatically creates a new folder when it clones a repo.

```
git clone https://github.com/bkeepers/github-notifications.git
cd github-notifications
```

By default, the project is set up to talk to github.com's endpoints. There are two files that you need to modify in order for the app to talk to your GitHub Enterprise system: `app/js/app.coffee` and `vim config/defaults.json`. Modifying these files is simple enough, just `vim` into them. (If you're more comfortable using a different text editor, go for it.)

```
vim app/js/app.coffee
```

Update the `endpoints` object to point to your GitHub Enterprise system. *Be sure to include the trailing slash at the end of these strings.*

```
vim config/defaults.json
```

Update the `oauth_client_id`, `oauth_client_secret`, and `oauth_host` properties of this object.

## Firing up the server

This project comes with some handy script files that make it ultra easy to set up the system.

```
script/bootstrap
```

This will install all of the node dependencies that this project uses. Once it's done setting everything up, fire up the server!

```
script/server
```

This will create a new HTTP listener server on port 8000. You should be able to hit the server from your own computer at this point! Try it out, and don't forget to include `:8000` at the end of the IP address or host name to force port 8000 use.

## Problems

There are a few problems with running this project on a GitHub Enterprise system. 

If your GHE instance isn't fully up to date, some of the APIs that this project takes advantage of will not be available.

Also, as far as I can tell, daemonizing a Node app is something that should happen in the app. This project doesn't have any daemonizing features.

Another thing: making config changes causes the git repo to be changed, so you will have trouble pulling new commits down. A workaround for this is to fork the project and run your fork.
