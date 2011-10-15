# Welcome to Renee!

*Renee is the super-friendly Rack based web framework.*

    :::ruby
    run Renee {
      path('/') { halt "Hello Renee!" }
    }

This site was been built using Renee and is [available on Github](https://github.com/renee-project/renee-site).

## Concept

Renee is a Rack-based DSL for expressing web applications. It seamlessly integrates with Rack to
let you mix and match with any other framework.

[&#8618; Read more](/concept)

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

Using Renee is as simple as understanding how to *configure settings*, *define routes*, and *respond to requests*, check
out detailed guides for each below:

[&#8618; Read about Configuration](/settings)

[&#8618; Read about Responding and Rendering](/responding)

[&#8618; Read about Routing](/routing)

[&#8618; Read about Route generation](/route-generation)

## API documentation

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