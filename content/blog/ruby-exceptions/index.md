---
title: "A Beginner's Guide to Exceptions in Ruby"
date: "2020-01-10T01:01:01.284Z"
category: "dev"
description: "How to use Ruby exception"
emoji: "👓"
---

If you want to continue the program even if an exception occurs for some reason, write the exception handling explicitly.

```ruby:title=exceptions_bad_example.rb
puts 'Start.'

module Greeter
  def hello
    'hello'
  end
end

begin 
  greeter = Greeter.new
rescue
  puts 'Exception occurred, but continue.'
end

puts 'End'
```

### Retry the process again when an exception occurs

```ruby:title=exceptions_retry_example.rb
retry_count = 0

begin
  puts 'Start the process'
  # Deliberately raise an exception
  1 / 0
rescue
  retry_count += 1
  if retry_count <= 3
    puts "Try again (#{retry_count} times.)"
    retry
  else
    puts "Failed to retry."
  end
end
```