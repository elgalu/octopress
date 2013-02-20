---
layout: post
title: "When to use Ruby inheritance versus mixins"
date: 2013-02-20 13:49
comments: true
categories: [ruby, inheritance, mixins]
published: true
---
In Ruby, I prefer to use [inheritance][] to establish a subtype from an existing object; to say for example that a Car is a kind of Vehicle.

But to use [mixins][] as a way to reuse code.
Car and Aircraf, for example, are both kind of vehicles but a Car may include a Wheeled mechanism while an Aircraft an Air::Skied one:

```ruby Car and Aircraf both vehicles but Car includes a Wheeled mechanism while Aircraft an Air::Skied
class Vehicle; end

class Car < Vehicle
  include TransportationMechanism::Land::Wheeled
end

class Aircraft < Vehicle
  include TransportationMechanism::Air::Skied
end
```

Decisions regarding what is what deeply depends on your [domain knowledge][] so you'll end up with a subjective solution hopefully producing a code nice to read then easy to understand.


[inheritance]: http://en.wikipedia.org/wiki/Inheritance_(computer_science)
[mixins]: http://en.wikipedia.org/wiki/Mixin
[domain knowledge]: http://en.wikipedia.org/wiki/Domain_knowledge
