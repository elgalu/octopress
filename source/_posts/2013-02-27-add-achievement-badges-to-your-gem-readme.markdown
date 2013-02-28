---
layout: post
title: "Add achievement badges to your gem README"
date: 2013-02-27 15:53
comments: true
categories: [ruby, gem, badges]
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

+ For the gem version badge just [push to rubygems] at least 1 time and the badge will be there for you

+ For your build status [Signup at Travis-CI](https://travis-ci.org/profile)

+ For gem dependencies status [Signup at Gemnasium](https://gemnasium.com/users/auth/github)

+ For code quality stats [Signup at Code Climate](https://codeclimate.com/github/signup)

+ For coverage percentage [Signup at Coveralls](https://coveralls.io/repos/new)

## Getting your code ready to use them

Gem version, code climate and gem dependencies do not require more work on your side besides pushing your updates to github, those services will take care of the rest.

### Travis-CI

Add this `.travis.yml` file to your project root, change it depending on the ruby versions you are interested to support:

```ruby
language: ruby
rvm:
  - 1.9.2
  - 1.9.3
  - 2.0.0
```

### Coveralls with SimpleCov

I use [SimpleCov] for test coverage, coveralls gem take care of your badge,

Add necessary dependencies to your gemspec:

```ruby
s.add_development_dependency "simplecov", ">= 0.7"
s.add_development_dependency 'coveralls', '>= 0.5.7'
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

Replace strings __elgalu__ with your github user and __boolean_class__ with your github repo name:

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

And don't forget to let me know if i'm missing new cool badges out there!


[thor]: https://github.com/wycats/thor#thor
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
