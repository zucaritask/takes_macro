# TakesMacro

[attr_extras][] is a **great** that lets you remove most of the boilerplate needed for creating different types of initializers in Ruby.

This gem contains a reimplementation of `pattr_initialize` from attr_extras that is much faster. If you're using attr_extras, but the only feature of it you're using is `pattr_initialize` then this gem is for you.

This gem calls the method `takes` to avoid confusing, but the API is exactly the same.

## Benchmark

Run `bundle exec ruby benchmarks/takes_macro_vs_attr_extras.rb` to see how much faster this gem is. The output from doing that on my machine is:

    $ bundle exec ruby benchmarks/takes_macro_vs_attr_extras.rb
    Warming up --------------------------------------
             attr_extras    15.793k i/100ms
                 takes_macro    91.596k i/100ms
    hand written initializer
                            71.351k i/100ms
    Calculating -------------------------------------
             attr_extras    169.353k (± 3.9%) i/s -    852.822k in   5.043680s
                 takes_macro      1.209M (± 4.5%) i/s -      6.045M in   5.011814s
    hand written initializer
                            875.721k (± 5.8%) i/s -      4.424M in   5.071249s

    Comparison:
                       takes_macro:  1208748.5 i/s
      hand written initializer:   875721.1 i/s - 1.38x  slower
                   attr_extras:   169352.8 i/s - 7.14x  slower

## How it works

This gem expands this

```ruby
class A
  takes [:foo!]
end
```

Into this

```ruby
class A
  def initialize(options)
    @foo = options.fetch(:foo)
  end

  attr_reader :foo
  private :foo
end
```

It does that by looking at the arguments to `takes` and from that building a `String` of Ruby code that it will then `class_eval`. That means calling `takes` is literally as fast as writing the initializer by hand. Nothing fancy happens when you call `A.new(...)`.

## Installation

Add this line to your application's Gemfile:

```ruby
gem "takes_macro"
```

And then execute:

    $ bundle

Or install it yourself as:

    $ gem install takes_macro

## Usage

If you want to use `takes` in all your classes add this to your app:

```ruby
require "takes_macro"

TakesMacro.monkey_patch_object
```

If you're in a Rails app I recommend adding this to `config/application.rb` so you're sure to have it in all your classes.

That will include the `TakesMacro` module, which defines the `takes` method, on `Object`.

If you don't like monkey patching `Object` you can still do this:

```ruby
require "takes_macro"

class A
  include TakesMacro

  takes [:foo!, :bar!, :baz!]
end
```

## Development

After checking out the repo, run `bin/setup` to install dependencies. Then, run `rake test` to run the tests. You can also run `bin/console` for an interactive prompt that will allow you to experiment.

To install this gem onto your local machine, run `bundle exec rake install`. To release a new version, update the version number in `version.rb`, and then run `bundle exec rake release`, which will create a git tag for the version, push git commits and tags, and push the `.gem` file to [rubygems.org](https://rubygems.org).

[attr_extras]: https://github.com/barsoom/attr_extras
