---
layout: post
title: "Using Bundler to add gem dependencies inline"
date: 2015-08-25T17:50:23+01:00
---

As Rails developers, we are used to using [Bundler][] within applications to manage Gemfile dependencies within our projects.

The conventional approach is to have a `Gemfile` in the root folder of our project, then use `bundle install` (and associated methods) to manage downloading, installation and version control of dependencies.

However, with version 1.10 of Bundler there is now a way for single-file scripts to include their own gemfile inline, and can optionally install the correct gems upon the script's first execution.

To use it, the command `require 'bundler/inline'` should be the first command in the file, followed by a `gemfile { ... }` block, the contents of which follow the usual Gemfile DSL:

{% highlight ruby %}
#!/usr/bin/env ruby
require 'bundler/inline'

# use `gemfile(true) do...` for the script to install missing gems
gemfile do
  source 'https://rubygems.org'
  gem 'json', require: false
  gem 'nap', require: 'rest'
  gem 'cocoapods', '~> 0.34.1'
end

puts Pod::VERSION # => "0.34.4"
{% endhighlight %}

(Example from the [`bundler/inline` documentation][docs]).

The downside to this approach is that no `Gemfile.lock` file is produced. As a result, we shoould probably be ultra-pessimistic when specifying version constraints on each gem.

For a small utility whose function can fit into a single script file, including the utility's gem dependencies within the same file could be useful.

[Bundler]: http://bundler.io
[docs]: https://github.com/bundler/bundler/blob/master/lib/bundler/inline.rb
