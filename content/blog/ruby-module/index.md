---
title: "[Ruby] Understanding the module"
date: "2020-01-09T02:01:01.284Z"
category: "dev"
description: "How to use Ruby module"
emoji: "👓"
---

In this article, I will show you how to use `module` in Ruby.

Let's look at an example.

Adding functionality by including a module in a class is called mix-in.  

```ruby:title=module_example.rb
module Loggable
  # Make the log method a private method.
  private

  def log(text)
    puts "[LOG] #{text}"
  end
end

class Product
  include Loggable

  def title
    log 'title is called'
    'A great movie'
  end
end

class User
  include Loggable

  def name
    log 'name is called'
    'Alice'
  end
end

product = Product.new
product.title

user = User.new
user.name
```

### Check for the presence of included modules.

```ruby:title=module_example.rb
is_include = Product.include?(Loggable)
p(is_include) # true

ancestors = Product.ancestors
p(ancestors) # [Product, Loggable, Object, Kernel, BasicObject]

included = product.class.included_modules # [Loggable, Kernel]
p(included)

is_a = product.is_a?(Loggable)
p(is_a)  # true
```

### Enumerable module

You can tell that an enumerable module is included by calling the `include?` method on the class.

If a class includes the enumerable module, it can call the `map`, `select`, `find` and `count` methods.

```ruby:title=enumerable_example.rb
Array.include?(Enumerable) # true
Hash.include?(Enumerable)  # true

[1, 2, 3].map { |n| n * 10 } # [10, 20, 30]

{ a: 1, b: 2, c: 3, }.map { |k, v| [k, v * 10] } # [[:a, 10], [:b, 20], [:c, 30]]
```

### Using modules to create namespaces

When you write  a class definition in the module syntax, it means "a class that belongs to that module".

When referring to a class that belongs to a module, use `ModuleName::ClassName`.

```ruby:title=namespace_example.rb
module Baseball
  class Second
    def initialize(player, uniform_number)
      @player = player
      @uniform_number = uniform_number
    end
  end
end

module Clock
  class Second
    def initialize(digits)
      @digits = digits
    end
  end
end

Baseball::Second.new('Alice', 13)
Clock::Second.new(13)
```

### Define module method Ruby

