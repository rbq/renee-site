# Welcome to Renee!

*Renee is the super-friendly Rack based web framework.*

    :::ruby
    run Renee {
      path('/') { halt "Hello Renee!" }
    }

This site was been built using Renee and is [available on Github](https://github.com/renee-project/renee-site).

## Concept (Why Renee?)

Renee is a new Rack-based library for expressing web applications. It seamlessly integrates with Rack to
let you mix and match with any other framework. [Josh Hull](https://github.com/joshbuddy) thought this up
when he started working on a new routing DSL for [Goliath](http://postrank-labs.github.com/goliath).

For us, Sinatra delivered a new and simpler way to think about building web applications.
The popularity of Sinatra both as a library and as a concept shows how enduring this shift has been.
Sinatra was different from Rails because the DSL was lightweight, easy to read and
combined routing and actions into just one file.

We wondered though, as Sinatra fans, if we were to come up with a cleaner and more powerful DSL, what might that look like?
Let's consider an example from Sinatra to see where we can improve upon this.

Consider:

    :::ruby
    get '/blog/:id' do
      Blog.get(params[:id])
    end

This is not too bad so far. The repetition of `:id` is a bit un-DRY, but not bad. Let's keep expanding upon this.

    :::ruby
    get '/blog/:id' do
      Blog.get(params[:id])
    end

    put '/blog/:id' do
      Blog.get(params[:id]).update_attributes(params)
    end

Now, we've retrieved blog in two places. Time to refactor. We'd normally create a before filter, with the same path.

    :::ruby
    before '/blog/:id' do
      @blog = Blog.get(params[:id])
    end

    get '/blog/:id' do
      @blog
    end

    put '/blog/:id' do
      @blog.update_attributes(params)
    end

Now we've repeated the same path three times. With Renee, we can describe these kind of ideas in a simple,
easy-to-read way. Here is the equivalent in Renee:

    :::ruby
    path 'blog' do
      var do |id|
        @blog = Blog.get(id)
        get { halt @blog }
        put { @blog.update(request.params); halt :ok}
      end
    end

This web library is inspired by Sinatra, but offers an approach more inline with Rack itself, and lets you
maximize code-reuse within your application.

## Getting started

### Installation

Renee is gem-based. If you're using rubygems, you can simply:

    $> gem install renee

If you're using [Bundler](http://gembundler.com/), you can add

    :::ruby
    gem 'renee', '~> 0.0.1'

to your `Gemfile`.

### Overview

Renee has (hopefully) a small number of keywords divided between several components:

* *Routing* is done either on the path, the query string, or other parts of the request headers.
* *Responding* makes it easy to respond to a request.
* *Rendering* gives you access to [Tilt](https://github.com/rtomayko/tilt) for rendering templates.
* *Rack interaction* makes it easy to call into [Rack](http://rack.rubyforge.org/)-based applications.
* *Request context* gives you access to the request and gives the basis for responding.

## Usage

Using Renee is as simple as understanding how to *configure settings*, *define routes*, and *respond to requests*.
Renee usage in a nutshell:

    :::ruby
    run Renee {
      path 'blog' do
        get  { render! "posts/index" }
        post { Blog.create(request.params); halt :created }
        var do |id|
          @blog = Blog.get(id)
          get { render! "posts/show" }
          put { @blog.update(request.params); halt :ok }
        end
      end
    }.setup {
      views_path "./views"
    }

Check out detailed guides for each aspect below:

[&#8618; Read about Configuration](/settings)

[&#8618; Read about Responding and Rendering](/responding)

[&#8618; Read about Routing](/routing)

[&#8618; Read about Route generation](/route-generation)

## API documentation

Renee is also well-documented with YARD:

[&#8618; renee](/doc/meta/index.html)

[&#8618; renee-core](/doc/core/index.html)

[&#8618; renee-render](/doc/render/index.html)

## Development

Renee's structure is pretty simple so far. The basic Rack DSL is contained in
[renee-core](https://github.com/renee-project/renee/tree/master/renee-core). This gem has no other dependencies other than Rack.

The rendering side is in [renee-render](https://github.com/renee-project/renee/tree/master/renee-render),
which depends on [Tilt](https://github.com/rtomayko/tilt).

The kitchen-sink gem which incorporates all of the others is [renee](https://github.com/renee-project/renee/tree/master/renee).
Please, any bugs, any ideas, I'd love to hear any of it. Love, [Team Renee](/team-renee). &hearts;