---
layout: post
title: "Console Ruby debug is easy"
date: 2013-03-01 13:11
comments: true
categories: [ruby, debug]
published: true
---
How many times have you thought that debugging is difficult so instead of finding out how to do it you simply started adding `puts "var value: #{some_var}"` all over your code and even external gems code!

Well i have news for you guys, ruby debug from your terminal is easy.

All you need to start is requiring `'debug'` when calling your ruby script or ruby binary:

    ruby -r debug your_program.rb

Below, take a look at how i debugged [thor][] gem to add a [new config option][]:

I executed some thor script with `ruby -rdebug script.rb` then:

```ruby
# Set a breakpoint at directory.rb line 80:
break ~/.rbenv/versions/2.0.0-p0/lib/ruby/gems/2.0.0/gems/thor-0.17.0/lib/thor/actions/directory.rb:80
# Now run until program ends or hits a breakpoint
cont
# Take a look at the local variables after hitting that breakpoint:
local_variables
#=> [:file_source, :file_destination, :dirname, :destination, :lookup]
file_source
#=> "~/.newgem-templates/default/.git/description"
```

Play around in that console as you normally do with irb and..

have fun!!

[thor]: https://github.com/wycats/thor
[new config option]: https://github.com/wycats/thor/pull/311
