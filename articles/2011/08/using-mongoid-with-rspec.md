<!--
author: JP Richardson
publish: Thu Aug 18 2011 18:47:07 GMT-0500 (CDT)
status: publish
type: post
link: https://procbits.wordpress.com/2011/08/18/using-mongoid-with-rspec/
tags: MongoDB, Rails, Ruby
slug: 2011/08/18/using-mongoid-with-rspec
title: Using Mongoid with Rspec
-->



I'm a fan of testing. I'm still trying to discipline myself to test
before I write code, that's been a tough habit to develop, however
testing after I develop a feature is a good habit that has paid
dividends. Lately I've started learning how to use Rspec for Rails.

Out of the box, Rspec is configured to use ActiveRecord. I've pretty
much stopped using relational databases in favor of NoSql solutions. My
favorite NoSql DB is MongoDB. The Ruby MongoDB ORM that I've been using
is [Mongoid](http://mongoid.org/).

To prep for Rspec, make your gemfile look like this (Assuming Rails
3.x):

```ruby
gem 'rspec-rails', :group => [:test, :development]
group :test do
  gem 'database_cleaner'
end
```

Then run: ` bundle install rails g rspec:install`

When I started learning how to use Rspec, I started having problems. The
first was the following error message:

`` undefined method `fixture_path=' for # (NoMethodError) ``

Oops, just needed to comment out the line spec\_helper.rb:

```ruby
config.fixture_path = "#{::Rails.root}/spec/fixtures"
```

Was I safe yet? Nope. Another error:
`` undefined method `use_transactional_fixtures=' for # (NoMethodError) ``

Oops, just needed to comment out the line spec\_helper.rb:

```ruby
config.use_transactional_fixtures = true
```

I should have known as the there are comments in the file.

Now, when I run my specs, the changes to the database persist from one
test to the next. Ideally, you want a clean database when you start each
test. This is where the gem
[database\_cleaner](https://github.com/bmabey/database_cleaner) comes in
handy.

Then add this to your spec\_helper.rb file:

```ruby
  config.before(:suite) do
    DatabaseCleaner[:mongoid].strategy = :truncation
  end

  config.before(:each) do
    DatabaseCleaner[:mongoid].start
  end

  config.after(:each) do
    DatabaseCleaner[:mongoid].clean
  end
```

That's it!

