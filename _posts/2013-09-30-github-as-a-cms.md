---
layout: post
title: GitHub as a CMS
---

This post was created and published entirely using GitHub. And it was easy. I'm using [Jekyll](http://jekyllrb.com) and [GitHub Pages](http://pages.github.com/), but you can set up this process to work with basically any flat file content management system. Also, this process isn't specific to blog posts. It can work for any kind of flat file content that your site uses.

## Don't reinvent the wheel

In a world where there are dozens and dozens (maybe even hundreds?) of content management systems, each somehow falling in between the range of stupidly simple and insanely complex, you need to evaluate your options carefully and determine which makes the most amount of sense for you and your project. During the research process, one of the most common selling points is the power and extensibility of the back-end admin interface.

Drupal is pretty popular and provides a great deal of customization options for the admin interface. Drupal doesn't have editorial review features out of the box, though, so just about every site has the [Workbench](https://drupal.org/project/workbench) module installed. This module gives admins the ability to delegate authority over specific parts of the website to individuals, and provides a good review process before the content is made live.

There are a few problems with it, though. In order for someone to propose a change, they need to have the following things:

* Access to the user creation page
* An account in the Drupal system
* Admin rights to the section they want to edit

For most large scale sites, the back-end user creation page is locked down. Accounts are manually created and admin rights are divvied up for stakeholders and content reviewers. A regular person inside the company can't even see how the content is set up, let alone propose a change to it.

## Power to the people

I believe that a support person who answers a phone call from customer who is confused about something they read on the site or a salesperson who has an idea on how to improve some messaging should have just as much power to propose a change to the content as a copy writer or a marketing manager. And it should be simple.

If the entire site - including and especially the content - lives in a single repository on GitHub, the process for anyone within the company to make a change is ridiculously easy, and all they need is a GitHub account.

Empowering anyone and everyone in the organization to propose changes to something as important as the public facing website displays a tremendous amount of transparency and openness. It also forces the project maintainers to keep everything as clean as possible to make it easier for people to make changes.

## The Process

1. To work on something new, fork the repo and create a new branch
2. Make whatever changes you want, commit and push to the server
3. Create a pull request (for CI and editorial review)
4. If the build passed and the changes look good, merge
5. Deploy the changes!

If this looks familiar, you may have been exposed to the [GitHub Flow](http://scottchacon.com/2011/08/31/github-flow.html) in the past. This process is exactly the same as that.

## Warning

This doesn't mean you should accept everything that has been proposed. Pull requests will come in that don't solve anything, or they're formatted improperly. It's the job of the project maintainers to keep it clean and make sure everything is good to go before it's merged.

Set up a CI system that crawls the site and runs tests for formatting and other functionally relevant things, such as broken links, images, and other stuff that might cause a site to behave improperly. Have a legitimate discussion about the proposed changes, and make sure they fall in line with the overall messaging of the brand. Make sure that it has passed all tests and it has been throughly reviewed before it is merged to `master`.
