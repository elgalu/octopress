---
layout: post
title: "Add achievement badges to your gem README"
date: 2013-12-14 19:02
comments: true
categories: [ruby, gem, badges, analytics]
published: true
---
I really enjoy crashing into a gem with a markdown-ready README file that includes top badges telling me things like the current available gem version, the fact that is continuously being tested using travis or knowing the developer cares about the gem dependencies being up-to-date!

Take a look how this looks like in [thor][] or some tiny [gem][boolean_class] of mine.

Achieving all that will take some steps, here is how:

## How the badges will look like

[![Gem Version][GV img]][Gem Version]
[![Build Status][BS img]][Build Status]
[![Dependency Status][DS img]][Dependency Status]
[![Code Climate][CC img]][Code Climate]
[![Coverage Status][CS img]][Coverage Status]

## Where to sign-up

+ For the gem version badge just [push to rubygems] at least 1 time and the badge will be there for you. Also, the "Version Badge" page offers an easy copy and paste builder of the URL that you can use [here][Version Badge]

+ For your build status go to [Travis-CI.org](https://travis-ci.org) and follow the link "Sign in with GitHub"

+ For gem dependencies status [Signup at Gemnasium](https://gemnasium.com/users/auth/github)

+ For code quality stats [Signup at Code Climate](https://codeclimate.com/github/signup)

+ For coverage percentage first [Signup once at Coveralls](https://coveralls.io/authorize/github) then for each of you projects go to the [Add Repo page](https://coveralls.io/repos/new)

+ For analytics [Signup at Google Analytics](http://www.google.com/analytics) and [Signup at githalytics][githalytics]

+ For pretty documentation generator read [here](http://documentup.com)
  or simply go to `documentup.com/your_github_user/your_repo_name`

## Getting your code ready to use them

Gem version, code climate and gem dependencies do not require more work on your side besides pushing your updates to github, those services will take care of the rest.

### Travis-CI

Add this `.travis.yml` file to your project root, change it depending on the ruby versions you are interested to support:

```ruby
language: ruby
rvm:
  - 1.9.3
  - 2.0.0
  - jruby-1.7.9
```

### Coveralls with SimpleCov

I use [SimpleCov] for test coverage, coveralls gem take care of your badge,

Add necessary dependencies to your gemspec:

```ruby
s.add_development_dependency "simplecov", ">= 0.8.2"
s.add_development_dependency 'coveralls', '>= 0.7.0'
```

If you use rspec, update your `spec_helper.rb` and add this lines to the very beginning:

```ruby
require 'simplecov'
require 'coveralls'

SimpleCov.formatter = SimpleCov::Formatter::MultiFormatter[
  SimpleCov::Formatter::HTMLFormatter,
  Coveralls::SimpleCov::Formatter
]
SimpleCov.start
```

## Update your README file

Replace strings __elgalu__ with your github user and __boolean_class__ with your github repo name.
It's probably a better idea that you use a templating tool for this like [the one I built and use](https://github.com/elgalu/gem-newgem)

    # TODO: Your gem name

    [![Gem Version][GV img]][Gem Version]
    [![Build Status][BS img]][Build Status]
    [![Dependency Status][DS img]][Dependency Status]
    [![Code Climate][CC img]][Code Climate]
    [![Coverage Status][CS img]][Coverage Status]

    ## Description

    TODO: Your gem description

    [Gem Version]: https://rubygems.org/gems/boolean_class
    [Build Status]: https://travis-ci.org/elgalu/boolean_class
    [travis pull requests]: https://travis-ci.org/elgalu/boolean_class/pull_requests
    [Dependency Status]: https://gemnasium.com/elgalu/boolean_class
    [Code Climate]: https://codeclimate.com/github/elgalu/boolean_class
    [Coverage Status]: https://coveralls.io/r/elgalu/boolean_class

    [GV img]: https://badge.fury.io/rb/boolean_class.png
    [BS img]: https://travis-ci.org/elgalu/boolean_class.png
    [DS img]: https://gemnasium.com/elgalu/boolean_class.png
    [CC img]: https://codeclimate.com/github/elgalu/boolean_class.png
    [CS img]: https://coveralls.io/repos/elgalu/boolean_class/badge.png?branch=master

Don't forget to let me know if i'm missing new cool badges out there!

## Google Analytics

I've recently added [githalytics][] service in order to have [Google Analytics][] for each of my github repos. Note there is also [bitdeli][] service but haven't tested it yet, let me know if is better or more free.

To do so:

1. Go to [Google Analytics][] website and signup to get your Property ID, e.g.

        Default URL protocol: https://
        Default URL source..: github.com/elgalu/time_ago_in_words

    You may also delete `github.com` from your GA **Referral Exclusion List**

2. Go to [githalytics][] and sign up, e.g.

        TrackingCode[url]:  https://github.com/elgalu/time_ago_in_words
        TrackingCode[code]: UA-12345-12

3. Add the fake image badge that will send data for analytics purposes, e.g.

        [![githalytics.com alpha](https://cruel-carlota.pagodabox.com/3fe2425a26ab0ca00b5bc6acf817af59 "githalytics.com")](http://githalytics.com/elgalu/time_ago_in_words)

That should be it. Remember to check later on Google Analytics site for the collected data, works like a charm!


[thor]: https://github.com/wycats/thor#readme
[boolean_class]: https://github.com/elgalu/boolean_class#booleanclass
[push to rubygems]: http://guides.rubygems.org/command-reference/#gem_push
[SimpleCov]: https://github.com/colszowka/simplecov

[GV img]: https://badge.fury.io/rb/boolean_class.png
[BS img]: https://travis-ci.org/elgalu/boolean_class.png
[DS img]: https://gemnasium.com/elgalu/boolean_class.png
[CC img]: https://codeclimate.com/github/elgalu/boolean_class.png
[CS img]: https://coveralls.io/repos/elgalu/boolean_class/badge.png?branch=master

[Gem Version]: https://rubygems.org/gems/boolean_class
[Build Status]: https://travis-ci.org/elgalu/boolean_class
[travis pull requests]: https://travis-ci.org/elgalu/boolean_class/pull_requests
[Dependency Status]: https://gemnasium.com/elgalu/boolean_class
[Code Climate]: https://codeclimate.com/github/elgalu/boolean_class
[Coverage Status]: https://coveralls.io/r/elgalu/boolean_class

[Google Analytics]: http://www.google.com/analytics/
[githalytics]: http://githalytics.com/
[bitdeli]: https://bitdeli.com/
[Version Badge]: http://badge.fury.io/for/rb/
