---
title:  "ObjectSpace::define_finalizer says no"
date:   2012-12-24 21:37:00 GMT
tags:   define_finalizer ObjectSpace ruby
---
In Ruby, one has to be extremely careful when using&nbsp;<code class="ruby">ObjectSpace::define_finalizer</code>. I fell for this trap:

```ruby
class C
  def initialize(id)
    @id = id
    self.class.finalize(self, id)
  end

  def self.finalize(obj, id)
    ObjectSpace.define_finalizer(obj, proc do
      puts "releasing a C with id = #{id}"
    end)
  end
end
```

See the problem here? Well, I didn't at first, until I noticed that my program only freed memory at shutdown. The problem here is that the `proc` is created with the `obj` instance as part of its scope... or `self`, even if it isn't used inside the `proc`, which is the infamous `ObjectSpace::define_finalizer` bug.

To correct this, I removed the `obj` parameter and called `ObjectSpace::define_finalizer` directly like so:

```ruby
class C
  def initialize(id)
    @id = id
    ObjectSpace.define_finalizer(self, self.class.finalize(id))
  end

  def self.finalize(id)
    proc do
      puts "releasing a C with id = #{id}"
    end
  end
end
```

This time around my computer started to say yes:

<iframe width="600" height="400" src="https://www.youtube.com/embed/WOdjCb4LwQY" frameborder="0"></iframe>

I hope this post may help someone, since I didn't find anything on the interwebs that exposed this specific issue.
