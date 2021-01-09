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