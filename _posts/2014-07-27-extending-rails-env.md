---
layout: post
title: Extending Rails.env
tags:
  - Ruby
  - Rails
---

Recently I needed a way to distinguish between different environments in an rails application I'am working on.
The application uses Amazons's CloudFront, an content delivery network, to reduce the traffic that hits the servers and to deliver static assets faster to the user.
But this should only happen in certain environments, more precisely not in the development or test environment (for now).

To achive this, I started out using the tools that rails provides:

```ruby
unless ["development", "test"].include? Rails.env
  # Do stuff for all environments except develoment and test
end
```

I recognized that this is a common pattern used in several places in the application. It was also likely to change.
Maybe after the changes were approved by the customer we won't need this behavior anymore for the testing environment.
What I wanted to do was to write the following code:

```ruby
unless Rails.env.live?
  # Do stuff for all environments except develoment and test
end
```

I archived this by adding a new initializer to the app:

```ruby
# config/initializer/env_extensions.rb

module EnvExtension
  def live?
    !["development", "test"].include? Rails.env
  end
end

Rails.env.extend(EnvExtension)
```

What this does is utilizing `Object#extend` to extent the Singleton Class (or Eigenclass) of the `Rails.env` without changing the original class behind `Rails.env` (whatever that might be).
But thats enought for my fist blog post. To learn more about Singleton Class I recommend reading [this](http://www.devalot.com/articles/2008/09/ruby-singleton) blog post by Peter J. Jones or
reading [Metaprogramming Ruby 2](https://pragprog.com/book/ppmetr2/metaprogramming-ruby) by Paolo Perrotta.
