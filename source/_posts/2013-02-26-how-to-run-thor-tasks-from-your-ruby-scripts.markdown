---
layout: post
title: "How to run thor tasks from your ruby scripts"
date: 2013-02-26 15:10
comments: true
categories: [ruby, thor]
published: true
---
If you have a thor task defined in some file like i do:

{% gist 5040554 newgem.thor %}

Normally, you would invoke this thor task *newgem* from the command-line like this:

    $ thor newgem some_name --test-framework rspec

But what if you need to do that from another ruby script?

Well, you can do so by passing the arguments directly, like this:

```ruby
args = ["some_name", "--test-framework", "rspec"]
Newgem.start(args)
```

Using [start][] is a way to go, but thor [documentation][] suggest using invoke() instead, like this:

```ruby
args = ["some_name"]
opts = {:test_framework => 'rspec'}
script = Newgem.new(args, opts)
script.invoke_all
```

Good look with your ruby scripting!

[start]: http://rdoc.info/github/wycats/thor/Thor/Base/ClassMethods#start-instance_method
[documentation]: https://github.com/wycats/thor/blob/master/lib/thor/base.rb#L423
