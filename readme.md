[![Travis](https://travis-ci.org/ga-dc/garnet.svg?branch=master)](https://travis-ci.org/ga-dc/garnet/)

# Garnet

Garnet is a collaboration by the instructors of GA's WDI to produce a student data-tracking app that's better than Google Sheets.

### Please fork, clone, and contribute!

### Current Features

  - Extensibility across courses and cohorts
  - Mobile-friendly attendance-taking
  - Integration with Github
    - Keep track of students' and instructors' Github information
    - Track issues, pull requests, and forks
  - A "Report Card" functionality that shows students their current attendance and assignment turn-in rates
  - Leave detailed notes on homework submissions in Markdown format
  - Leave detailed observations on students in Markdown format
  - Admin and Non-Admin roles

## Local Setup

1. `$ git clone https://github.com/ga-dc/garnet`
- `$ cd garnet`
- Install headless browser for js features
  - `$ brew install phantomjs`
  - If you get an error about installing on Yosemite (or later), we need phantom 2.x.  Download the latest 2.x from https://github.com/Vitallium/phantomjs/releases and copy `phantomjs` to `/usr/local/bin`.
- `$ bundle install`
- `$ rake db:create`
- `$ rake db:schema:load`
  - Note: migrations are **not** designed to be run from a new DB
- `$ rake db:seed`
  - Note: this generates a decent amount of data and can take up to 1 minute
- `$ bundle exec figaro install`
- [Register a Github application](https://github.com/settings/applications) and update `config/application.yml` to look like this:

    ```
    gh_client_id: "12345"
    gh_client_secret: "67890"
    gh_redirect_url: "http://localhost:3000/github/authenticate"
    ```

9. `$ rspec -f d`
10. `$ rails s`
12. Log in with username `adam` and password `password`
13. Enter the Konami code

# Models

![The ERD](app/assets/images/Garnet_ERD.png)

## Github.rb

The Github model has an API method. To use it:

```rb
# Gets the current organization's repos
request = Github.new(ENV).api.repos

# Gets the current user's repos
request = Github.new(ENV, session[:access_token]).api.repos
```

It's built on the Octokit gem. For more information [see the Octokit docs](https://github.com/octokit/octokit.rb).

Users can sign up *with* or *without* Github.

If they sign up *with* Github, they cannot update their username, password, e-mail, etc. Every time they subsequently sign in, the Github API is polled for their most recent information, and the database is updated accordingly.

If they sign up *without* Github, they can update their username, password, e-mail, etc. Should they wish to later link Github to their account, they can click the "Link Github account" link, which will poll the database, rewrite their information in the Users table to use their Github username, email, etc. From there their account will behave as if they had originally signed up with Github.

# Deployment on Heroku

When a pull request is made against `master` or commits are pushed to an existing pull request to `master` on this repo, [Semaphore](https://semaphoreci.com/) will run the tests specified in `spec/`.

If all tests pass, the application will be deployed to the staging application on Heroku.

Pull requests should have a successful Semaphore build and an approved review on GitHub prior to being merged.

After testing the staging application, you can promote the application from staging to production.

## Managing the Deployment

You'll need to reach out to someone from engineering to gain Heroku dashboard access Taylor Kems, Nick Wilson, or someone from the GA Engineering team (Slack channel `#engineering`).

# Production Pipeline

The production pipeline is owned by the GA tech account, managed in part by the engineering team. GArnet contributors can be made into Pipeline collaborators if necessary. Please contact the engineering team.

> [Heroku Docs](https://devcenter.heroku.com/articles/pipelines#deployment-with-pipelines)

## Warning about the Asset Pipeline and `ENV`

If you interpolate `ENV` values, asset compilation will result in these values being hardcoded in `.css` and `.js` files, since the object references will be evaluated at and then 'hardcoded' when assets compile. Beware of this!

## Metrics

> `heroku run rake -T metrics`

## Scheduled Tasks
- The heroku scheduler addon will run the following two `rake` tasks: `attendance:mark_absent` at 12:00PM (EST) and `bundle exec rake metrics:generate` at 1:00AM (EST)

### Sandi Metz rules

- sandi_meter outputs to "/metrics/sandi_meter"
- https://github.com/makaroni4/sandi_meter

## NewRelic

New Relic monitors the app and provides metrics.  They are available in development mode (/newrelic) and production (rpm.newrelic.com).  It is recommended that you [install newrelic-sysmond on the servers](https://rpm.newrelic.com/accounts/1130222/servers/get_started).

"config/newrelic.yml" was downloaded from rpm.newrelic.com and updated to use `<%= app_name %>`

## RSpec

Use it!

## CLI

### Setup

We have created a garnet function that needs to be "sourced" to be available from the CLI.

1. Use the provided garnet shell file:
  - Either, symlink to your local copy (updated automatically, as you `pull` changes):
  ```
  ln -s absolute_path/to/garnet/garnet ~/.garnet
  ```
  - Or, Download from github:
  ```
  curl https://raw.githubusercontent.com/ga-dc/garnet/cli/garnet > ~/.garnet
  ```
    - Note: to get updates (using this method), just re-download the latest file, replacing the current file.
- Update your ~/.bash_profile to "source" this file (only do this once).
  - e.g. `echo 'source ~/.garnet' >> ~/.bash_profile`


### CLI Usage

Once the file has been sourced, the function is available from terminal.

```
$ garnet logs
```
---

## Thanks, ever so much, to *all* our contributers

Code Contributors: 16
```
adambray (Adam Bray)
amaseda (Adrian Maseda)
aspittel (Ali Spittel)
andrewsunglaekim (Andrew Kim)
beckybeauchamp1 (Becky Beauchamp)
ebirving (Erica Irving)
jshawl (Jesse Shawl)
joe-gz (Joe Glatman Zaretsky)
jmas13 (John Master)
JonRojas (Jon Rojas)
mattscilipoti (Matt Scilipoti)
nolds9 (Nicholas Olds)
RobertAKARobin (Robert Thomas)
tailtore (Taylor Kems)
tylercrosse (Tyler Crosse)
esfourteen (Nick Wilson)
```

Non-coding contributors: 8
```
dan-ator (Daniel Alexander)
tessb (Tess B)
sarahbrookscoach (Sarah Brooks)
ShanazFC (Shanaz Chowdhery)
timfoley (Tim Foley)
jocelynhoule (Jocelyn Houle)
bmartinowich (Brian Martinowich)
jenkins-ga (RIP)
```

 This contributor list was brought to you by [octoHatRack](https://github.com/LABHR/octohatrack) feat. various humans.
