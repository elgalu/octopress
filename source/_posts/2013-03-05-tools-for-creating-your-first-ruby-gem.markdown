---
layout: post
title: "Tools for creating your first ruby gem"
date: 2013-03-05 11:24
comments: true
categories: [ruby, gem_creation, rubygems, yard, rspec, twitter, twitter_anonymous_client, gem-newgem]
published: true
---
This post is about tools you can use to turn your ruby idea into a ruby gem so you can share it with the ruby world.
I will use a tiny gem of mine [twitter_anonymous_client][] as a practical example and also use some power tools like [yard][] that may be overkill for this tiny project but nice to know when working on bigger projects later on.

## Gem Creation

You have a couple of options to start your gem files and directory structure, you can choose to write all of if from scratch, copy & paste from another gem source you liked, or use a bootstrap tool that does the work for you.

A popular option is using [bundle gem][] *GEMNAME* to bootstrap your gem. I started with bundler but right after the third gem i got tired of being fixed to a template not so easy to customize without sticking my nose around [bundler][bundler predefined] source.

So i partially extracted that into a gem called [gem-newgem] and turned it into a RubyGems plugin which is what i will use in this post.

### Bootstrap your gem

```bash
$ gem install gem-newgem
Successfully installed gem-newgem-0.1.1

$ gem new twitter_anonymous_client --summary 'Twitter public (anonymous) client for old v1.0 API'
      create  twitter_anonymous_client
      create  twitter_anonymous_client/twitter_anonymous_client.gemspec
      create  twitter_anonymous_client/.gitignore
      create  twitter_anonymous_client/.rspec
      create  twitter_anonymous_client/.travis.yml
      create  twitter_anonymous_client/CHANGELOG.md
      create  twitter_anonymous_client/Gemfile
      create  twitter_anonymous_client/LICENSE.md
      create  twitter_anonymous_client/README.md
      create  twitter_anonymous_client/Rakefile
      create  twitter_anonymous_client/lib/twitter_anonymous_client.rb
      create  twitter_anonymous_client/lib/twitter_anonymous_client/version.rb
      create  twitter_anonymous_client/spec/twitter_anonymous_client_spec.rb
      create  twitter_anonymous_client/spec/spec_helper.rb
INFO: Initializing git repo at twitter_anonymous_client/
  git init
Initialized empty Git repository in ~/twitter_anonymous_client/.git/
  git add .
INFO: Will add remote so you get ready to push to github
  git remote add github git@github.com:elgalu/twitter_anonymous_client.git
INFO: Make branch tracking automatic
  git config --add branch.master.remote github
  git config --add branch.master.merge refs/heads/master

$ cd twitter_anonymous_client
```

If you take a look at the files you will see how your `~/.gitconfig` information is used, the gem name you provided and the summary are used even in the README.md file.

This is how my [.gemspec][] was generated, you'll soon find out this is opinionated stuff, feel free to modify the [template][] found at `~/.newgem-templates` to get the requirements you need:

```ruby .gemspec
require 'twitter_anonymous_client/version'
Gem::Specification.new do |spec|
  spec.platform      = Gem::Platform::RUBY
  spec.name          = "twitter_anonymous_client"
  spec.version       = TwitterAnonymousClient::VERSION
  spec.summary       = %q{Twitter public (anonymous) client for old v1.0 API}
  spec.description   = spec.summary

  spec.required_ruby_version     = '>= 1.9.2'
  spec.required_rubygems_version = '>= 1.8'

  spec.license       = 'MIT'

  spec.authors       = ["Leo Gallucci"]
  spec.email         = ["elgalu3@gmail.com"]
  spec.homepage      = "https://github.com/elgalu/twitter_anonymous_client"

  spec.executables   = spec.files.grep(%r{^bin/}).map{ |f| File.basename(f) }
  spec.files         = `git ls-files`.split($/)
  spec.test_files    = spec.files.grep(%r{^(test|spec|features)/})
  spec.require_paths = ["lib"]

  spec.add_development_dependency "bundler", ">= 1.2"
  spec.add_development_dependency "rake"
  spec.add_development_dependency "rspec", "~> 2.13"
  spec.add_development_dependency "redcarpet", ">= 2.2"
  spec.add_development_dependency "yard", ">= 0.8"
  spec.add_development_dependency "simplecov", ">= 0.7.1"
  spec.add_development_dependency 'coveralls', '>= 0.5.8'
end
```

I will just add one runtime dependency there:

```ruby Add to .gemspec
  spec.add_runtime_dependency "strongly_typed"
```

By looking at the README.md file, you'll see how much i like putting badges at the top of the doc.
You will need to sign-up to those services to get your gem up and running, i wrote another [post][post on badges] on how to do it.

### Spec first, code later

First and foremost, check everything is working with bundler and rspec:

```bash
# Why not, use the latest available ruby version (as of this writing)
$ ruby -v
ruby 2.0.0p0 (2013-02-24 revision 39474) [x86_64-linux]

# Get your Gemfile.lock ready
$ bundle install
Using ...
Installing ...
Using twitter_anonymous_client (0.0.1) from source ...

# Let's see if some rake tasks are already available
$ bundle exec rake -T
rake build    # Build twitter_anonymous_client-0.0.1.gem into the pkg directory.
rake install  # Build and install twitter_anonymous_client-0.0.1.gem into system gems.
rake release  # Create tag v0.0.1 and build and push twitter_anonymous_client-0.0.1.gem to Rubygems
rake spec     # Run RSpec code examples

# Check the default task is to run the spec's
$ bundle exec rake
...
TwitterAnonymousClient
  should have a version number

Finished in 0.00537 seconds
1 example, 0 failures
...
```

Everything nice so far!

Now is time to write about what we expect from this gem:

Create a spec file like `spec/user_timeline_spec.rb`

```ruby spec/user_timeline_spec.rb
require 'spec_helper'

describe Twitter::Client do
  let(:screen_name) { 'elgalu' }
  let(:client) { Twitter::Client.new }
  let(:tweets) { client.user_timeline(screen_name, count: 1) }
  let(:first_tweet) { tweets.first }

  describe '#user_timeline' do
    context 'first tweet' do
      it 'should be a Tweet' do
        first_tweet.should be_a(Twitter::Tweet)
      end

      it 'should respond to id, text, created_at' do
        first_tweet.should respond_to(:id)
        first_tweet.should respond_to(:text)
        first_tweet.should respond_to(:created_at)
      end
    end
  end
end

```

Let's leave out web mocking for now and expect those specs to run against real twitter servers.

Run `$ bundle exec rake spec` and watch it fail.

### Code to make the specs pass

Good luck with that! .... or ... take a look at [twitter_anonymous_client][] source code.

## Add documentation using [yard][]

Yard is really nice producing beauty documentation files, all you need to do is remember a few keywords:

```ruby Yard Doc Example http://rubydoc.info/gems/twitter_anonymous_client/Twitter/API/Timelines#user_timeline-instance_method
# Get some user timeline by screen name (last statuses)
#
# @param [String] screen_name the twitter user slug
# @param [Hash] opts the options to retrieve the statuses
# @option opts [Integer] :count The number of statuses to retrieve
#
# @example
#   Twitter::Client.new.user_timeline('DolarBlue', count: 1)
#   #=> [#<Twitter::Tweet:0x011.. @id="308609..., @text="Dolar Paralelo: $7,84.....
def user_timeline(screen_name, opts)
  .......
```

You should always check how your documentation looks like from time to time
Run yard server! i will pass a different port cause i'm already using 8808 with something else:

```bash
$ bundle exec yard server --port 8828 --reload

[info]: No yardoc db found in .yardoc, parsing source before starting server...
Files:           2
...
Methods:         1 (    0 undocumented)
 33.33% documented
>> YARD 0.8.4.1 documentation server at http://0.0.0.0:8828
...
```

Open your browser at (http://localhost:8828) and find some of your type errors ;)

## Publish your gem

Ensure you have your github username configured:

    git config --global github.user your_user_name

### Only the first time on the your gem:

    curl -u your_user_name https://rubygems.org/api/v1/api_key.yaml > ~/.gem/credentials
    ### Enter host password for user 'your_user_name': ......

### Every time you push a gem release

Follow these steps

```bash
# Commit your changes
git add ... && git commit ...

# Run specs on each ruby version you support to make sure everything works
rbenv shell 2.0.0-p0
bundle exec rake spec
rbenv shell 1.9.3-p392
bundle exec rake spec

# Separate commit to bump version
git add ... version.rb
git commit -m 'Bump version to 0.0.1'

# Tag your release
git tag -a v0.0.1 -m 'Version 0.0.1'

# Build your gem
gem build twitter_anonymous_client.gemspec

# Push to github
git remote add origin git@github.com:elgalu/twitter_anonymous_client.git

# Make tracking automatic
git config --add branch.master.remote origin
git config --add branch.master.merge refs/heads/master

# Push updates and tags
git push origin master
git push origin master --tags

# Push to rubygems
gem push twitter_anonymous_client-0.0.1.gem
#=> Pushing gem to https://rubygems.org...
#=> Successfully registered gem: twitter_anonymous_client (0.0.1)
```

Now tell ma you have your own ruby gem!


## More info

If you need to go down into more details you can check:

- [radar guide on gem-development](https://github.com/radar/guides/blob/master/gem-development.md)
- [RubyGems guide](http://guides.rubygems.org/make-your-own-gem/)
- [Jeweler vs Bundler for gem development](http://mlomnicki.com/ruby/rubygems/2011/01/22/jeweler-vs-bundler.html)


[.gemspec]: https://github.com/elgalu/twitter_anonymous_client/blob/master/twitter_anonymous_client.gemspec
[twitter_anonymous_client]: https://github.com/elgalu/twitter_anonymous_client

[yard]: http://yardoc.org/
[bundle gem]: http://gembundler.com/v1.2/bundle_gem.html
[bundler predefined]: https://github.com/carlhuda/bundler/tree/master/lib/bundler/templates/newgem
[template]: https://github.com/elgalu/newgem-template
[post on badges]: http://elgalu.github.com/2013/add-achievement-badges-to-your-gem-readme/
[gem-newgem]: https://github.com/elgalu/gem-newgem
