# Responding

## A note about bang methods

Any method that ends with ! (a bang method) will do whatever the non-bang version of that method will do,
but in addition, it will halt with that response. (With the notable exception of `#halt!` itself.)

## `#halt` (and `#halt!`)

Halt will immediately stop processing and respond with the value passed to it. It will respond
in different ways depending on what was passed to it. Halt! will behave like halt, but it doesn't
require the path to be entirely consumed before sending a response.

### `Symbol`

If it's a symbol, it will attempt to look it up in `Renee::Core::Application::Responding::HTTP_CODES`.

    :::ruby
    # Return status with symbol
    halt :not_found

### `String`

It will use this to create a Rack::Response object and call `#finish` on it.

    :::ruby
    # Return 200 with body
    halt "hello!"

### `Array`

It will attempt to interpret this as a Rack response.

    :::ruby
    # Return 200 with body
    halt [200, {}, 'body']

### `Integer`

It will return this as a status code.
    
    :::ruby
    # Return just 200 status code
    halt 200

## `#respond` (and `#respond!`)

The `respond` command makes returning a rack response very explicit,
you can respond as if you were constructing a Rack::Response

    :::ruby
    run Renee {
      get { respond!("hello!", 403, "foo" => "bar") }
    }

or use the block DSL for convenience:

    :::ruby
    run Renee {
      get { respond! { status 403; headers :foo => "bar"; body "hello!" } }
    }

## `#redirect` (and `#redirect!`)

This will return a rack-response to the path supplied and an optional HTTP status code.

    :::ruby
    get {
      redirect!('/hello')
    }

You can also specify the status code for the redirect:

    :::ruby
    get {
      redirect!('/hello', 303)
    }

## `#render` (and `#render!`)

This will render a template based on the rendering options supplied. The simplest render is just:

    :::ruby
    render! "index"

Assuming you have a views_path setup (see [Settings](/settings)), this will render the first template named `index`.
You can also specify the template engine explicitly with:

    :::ruby
    render! "index.haml"

This will locate and render the haml template (`index.haml`) within views. You can also pass local variables to
the template:

    :::ruby
    render! "index", :locals => { :foo => "bar" }

and then access them as expected within the template. Using a layout is also as simple as specifying the
layout template with a render:

    :::ruby
    render! "index", :layout => "base"

This will render the index template within the base template in the views folder.