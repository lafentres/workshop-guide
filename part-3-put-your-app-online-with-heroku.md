# Part 3: Put your app online with Heroku

#### Get Heroku <a id="get-heroku"></a>

Follow steps “Introduction” and “Set up” of the [Getting Started on Heroku with Ruby](https://devcenter.heroku.com/articles/getting-started-with-ruby#introduction) to sign up, install the Heroku CLI, and login.

Right now it looks like the download link for OpenSSL on the Heroku page is broken.  [Here's what you're looking for](https://slproweb.com/products/Win32OpenSSL.html) for Windows.

**COACH**: Talk about the benefits of deploying to Heroku vs traditional servers.

#### Preparing your app <a id="preparing-your-app"></a>

**Version Control Systems**

We need to add our code to version control. You can do this by running the following in the terminal:

```text
git init
git add .
git commit -m "initial commit"
```

**COACH**: This would be a good time to talk about version control systems and git.

**Updating our database**

First, we need to get our database to work on Heroku, which uses a different database. Please change the following in the Gemfile:

```ruby
gem 'sqlite3'
```

to

```ruby
group :development do
  gem 'sqlite3'
end
group :production do
  gem 'pg'
end
```

Run `bundle install --without production` to setup your dependencies.

Don’t forget to do `git add .` and `git commit -m "Use postgres as production database"` in order to add this change to your master branch before pushing to heroku later.

**COACH**: You can talk about RDBMS and the different ones out there, plus include some details on Heroku’s dependency on PostgreSQL.

#### Deploying your app <a id="deploying-your-app"></a>

**App creation**

We need to create our Heroku app by typing `heroku create` in the terminal and see something like this:

```text
Creating app... done, ⬢ young-reaches-87845
https://young-reaches-87845.herokuapp.com/ | https://git.heroku.com/young-reaches-87845.git
```

In this case “young-reaches-87845” is your app name.  It's randomly generated; yours will different than this one in the guide, and your neighbors.

Because this project uses an older, but still common, version of ruby, we'll have to tell Heroku we want to use an older stack on their servers by typing 

```text
heroku stack:set heroku-16
```

**Pushing the code**

Next we need to push our code to heroku by typing `git push heroku master`. You’ll see push output like the following:

```text
Counting objects: 115, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (97/97), done.
Writing objects: 100% (115/115), 25.62 KiB | 0 bytes/s, done.
Total 115 (delta 10), reused 0 (delta 0)
remote: Compressing source files... done.
remote: Building source:
remote:
remote: -----> Ruby app detected
remote: -----> Compiling Ruby/Rails
remote: -----> Using Ruby version: ruby-2.2.4
remote: -----> Installing dependencies using bundler 1.11.2
remote:        Running: bundle install --without development:test --path vendor/bundle --binstubs vendor/bundle/bin -j4 --deployment
remote:        Fetching gem metadata from https://rubygems.org/..........
remote:        Fetching version metadata from https://rubygems.org/...
remote:        Fetching dependency metadata from https://rubygems.org/..
remote:        Installing concurrent-ruby 1.0.2
...
remote: -----> Launching...
remote:        Released v5
remote:        https://young-reaches-87845.herokuapp.com/ deployed to Heroku
remote:
remote: Verifying deploy... done.
To https://git.heroku.com/young-reaches-87845.git
 * [new branch]      master -> master
```

You’ll know the app is done being pushed, when you see the “Launching…” text like above.

**Migrate database**

Next we need to migrate our database like we did locally during the workshop:

```text
heroku run rails db:migrate
```

When that command is finished being run, you can hit the app based on the url. For this example app, you can go to [https://young-reaches-87845.herokuapp.com/](https://young-reaches-87845.herokuapp.com/). You can also type `heroku open` in the terminal to visit the page.

**Closing notes**

Heroku’s platform is not without its quirks. Applications run on Heroku live within an ephermeral environment — this means that \(except for information stored in your database\) any files created by your application will disappear if it restarts \(for example, when you push a new version\).

**Ephemeral filesystem**

> Each dyno gets its own ephemeral filesystem, with a fresh copy of the most recently deployed code. During the dyno’s lifetime its running processes can use the filesystem as a temporary scratchpad, but no files that are written are visible to processes in any other dyno and any files written will be discarded the moment the dyno is stopped or restarted. For example, this occurs any time a dyno is replaced due to application deployment and approximately once a day as part of normal dyno management.

In the [App](part-1-build-your-first-app.md#4-adding-picture-uploads) tutorial the ability to attach a file to the Idea record was added, which results in new files being written to your applications`public/uploads` folder. The ephemeral storage in Heroku can be seen with the following steps:

1. Launch the app with `heroku open`
2. Add a new Idea with an image
3. Restart the application by running `heroku restart`
4. Go back to your Idea and reload the page - the image should no longer be visible

**Working around Ephemeral Storage**

Obviously this doesn’t seem to be useful if you were running a real life application, but there are ways to work around this which is commonly used by a lot of popular websites.

The most common method is to use an external asset host such as Amazon S3 \(Simple Storage Service\) or Rackspace CloudFiles. These services provide \(for a low cost

* usually less then $0.10 per GB\) storage ‘in the cloud’ \(meaning the files could potentially be hosted anywhere\) which your application can use as persistent storage.

While this functionality is a bit out of scope for this tutorial there are some resources available which you can use to find your way:

* [How to: Make Carrierwave work on Heroku](https://github.com/carrierwaveuploader/carrierwave/wiki/How-to%3A-Make-Carrierwave-work-on-Heroku)
* [Amazon S3 – The Beginner’s Guide](http://www.hongkiat.com/blog/amazon-s3-the-beginners-guide/)

As always if you require any more information or assistance your coaches will be able to assist.

