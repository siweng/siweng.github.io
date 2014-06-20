---
layout: post
title: Close to Production
---

This site uses [Jekyll](http://jekyllrb.com) and is hosted by the gracious folks at GitHub via their awesome [GitHub Pages](http://pages.github.com/) service. It's a pretty sweet setup; create a repository that matches a certain pattern and start pushing commits to it. GitHub automatically notices any push made to the repo, rebuilds the site using the `jekyll build` command, and then serves it up.

As a developer building stuff in a continuous integration system, the goal should be to create an environment that matches as close to production as possible so you can run tests on the code before it gets shipped to a production server for the world to see. Luckily, there are some really, really cool tools out there that make this relatively easy.

## Bundler

The self-proclaimed "best way to manage your application's dependencies" is [Bundler](http://bundler.io/), and they'll get no argument from me. Bundler lets you create a list of gems that your app needs in order to build successfully, and then in one simple command, installs them.

GitHub provides a [gem](http://rubygems.org/gems/github-pages), which makes it super easy to pull down all of the dependencies that the system uses to generate the site. To get it going, create a `Gemfile` and include it in there:

```
source 'https://rubygems.org'
gem 'github-pages', '~> 6'
```

Next, just run `bundle install` and all of the dependencies will magically install. This is probably the simplest `Gemfile` known to man, but hopefully you can appreciate the elegance of defining the gems that your app needs within the repository, so that anyone who clones it will have everything they need to get started, including any third-party services that you decide to use.

## Travis CI

One of my favorite tools that I am finding any excuse to play with is [Travis CI](http://travis-ci.org/). If you're not familiar with it, it's a continous integration service that integrates directly with GitHub. Any time a new commit is pushed to a repo that it is watching, a new worker instance is created and a configuration is run. You are able to specify commands in the configuration file and the worker process will just run them. And it's free.

To get Travis started, drop some configuration stuff into a `.travis.yml` file:

```yaml
language: ruby
rvm:
  - 1.9.3 # the github-pages gem requires 1.9.3
install:
  - bundle install # install the dependencies defined in the Gemfile
script:
  - jekyll build --trace # build the site and give details about any errors
```

This will tell Travis CI to set up the worker to use Ruby 1.9.3, install the stuff defined in the `Gemfile`, and eventually build, serve, and detach the web server process from the command line interface. If anything experienced problems during the build, that command would have exited with an error code and Travis would have registered the build a failure.

## Conclusion

At the very least, this gives you a good idea on how to debug the build process as close to the production system that GitHub Pages uses. At most, you can execute browser based tests using [CasperJS](http://casperjs.org/) to make sure your site doesn't have any broken links, there is only one `<h1>` tag on each page, and any other crazy thing you can think of.

Enjoy!
