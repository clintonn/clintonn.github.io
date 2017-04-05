---
title: Debugging with `begin`
date: 2017-03-22 15:00:00 Z
categories:
- code
layout: post
---

Let's say every time you left the house and the door jammed the house just toppled. That's definitely no way to live. But for any Ruby or Rails program that doesn't have proper error handling, that's likely what happens when the program expects one thing and doesn't know how to handle the other.

The dreaded undefined method `[]' for nil:NilClass (NoMethodError) was a frequent error when I made [Maestro](https://github.com/curiositypaths/maestro) and every time it happened, it'd shut down the process, totally throw my Heroku dyno for a loop, and cause general chaos.

Even if you abide by "move fast and break things" mantra that gave Facebook its moxie early on, you should always draw a fine line between flat out failing and failing gracefully.

Here's how to do that in Ruby.

In Ruby, the couple blocks you need to know for error handling (exception handling everywhere else) are the following:

* `begin`
* `rescue`
* `raise`

A `begin` block essentially defines a block of code, that, when it produces an error, will pass the execution flow to a `rescue` block that will handle the error more specifically. If you have any lines of code that have deeply embedded logic, look smelly or messy, or otherwise could raise an error in your program, the best thing to do is to enclose it in a `begin` block and rescue it as you see fit.

Ruby already has a couple tools available do runtime debugging in a dev environment — namely [pry](https://rubygems.org/gems/pry/versions/0.10.4) and [byebug](https://rubygems.org/gems/byebug). But a good practice to get used to is putting suspect code in a begin block and catching generic exceptions, so that it doesn't kill off your thread.

So for instance:

```ruby
require 'byebug-pry'
def do_something_weird(input)
  input.each do |key|
    begin
      key.errorable_method
    rescue
      binding.pry
    end
  end
end
```

Don't actually do this on deployment — the rescue block will capture anything under the umbrella of the `StandardError` class, which is most everything that's not fatal. You don't want to rescue every error in the same way.

In an actual production build, you'd obviously want to use specific [Error classes](https://ruby-doc.org/core-2.2.0/Exception.html) or make a custom subclass under `StandardError` to better signal what went wrong.

```ruby

class CustomError < StandardError
  def initialize(msg="This error is more specific!")
    super
  end
end

def main
  begin
    a = gets.chomp
    do_something_weird(a)
  rescue CustomError
    # do something specific to CustomError
  end
end

```

Even better, you'd want logs so you have a running record of issues that run through the lifetime of the program. Ruby has the helpful `Logger` class [to deal with this](https://ruby-doc.org/stdlib-2.2.3/libdoc/logger/rdoc/Logger.html).

```ruby
# main.rb

require 'logger'
logger = Logger.new('main.log')
logger.debug "Logger created for main.rb"
```

After you instantiate a new logger for the file, it'll keep a running log of errors and warnings. You can set the logger's level so that it only captures messages of a minimum priority.

```ruby
logger.level = Logger::WARN
```

I imagine there'll be plenty of use for capturing errors in the future, as userbases grow to be a little more mischievious. Though Rails has a lot of in-built security features and validations that don't crash the server, the biggest killer in the end could just be your own logic errors. 

#### Resources
* [Ruby Docs — Logger Class](http://ruby-doc.org/stdlib-1.9.3/libdoc/logger/rdoc/Logger.html)
* [Byebug](https://github.com/deivid-rodriguez/byebug)
* [StandardError default to the rescue](https://airbrake.io/blog/ruby-exception-handling/standarderror)
* [Custom exceptions in Ruby](http://blog.honeybadger.io/ruby-custom-exceptions/)
