# Routing

## Rack integration

### `#run` (and `#run!`)

This method will take whatever is passed to it and send `#call` to it.

### `#build` (and `#build!`)

This method will take run the code block passed to it and wrap it with a Rack::Builder, then run it.

Example:

    :::ruby
    Renee {
      build! {
        use Rack::ContentLength
        run proc {|env| [200, {}, ["hello rack!"]]}
      }
    }

## Request context

The most basic part of Renee is the request context. This gives you access to the current env,
a Rack::Request wrapped version of the env.

## Path-based

All path based routing proceeds by removing the matched part from `PATH_INFO`, and adding it to the end of
`SCRIPT_NAME` before executing the block passed to it. Therefore, only the path based routing method mutate
the rack environment, and they are the only methods that do so.

### `#path`

This will match the part supplied to it.

Example:

    :::ruby
    Renee {
      path('test') {
        halt 'i got /test!' # your path must start with /test to get here.
      }
    }

Paths can also be nested within one another:

    :::ruby
    Renee {
      path('blog') {
        path('foo') { get { halt "path is /blog/foo" } }
      }
    }

If the part supplied doesn't start with a `/`, one will be added. If there is a trailing slash, and consuming it would
cause `PATH_INFO` to be empty, it will be consumed as well. In the above example, `/test/` would also match.

### `#exact_path`

This is identical to `#path`, except trailing slashes will not be matched. For example:

    :::ruby
    Renee {
      exact_path('test') {
        halt 'i got /test!' # your path must be /test to get here.
      }
    }

### `#whole_path`

This is identical to `#path`, except it will not execute the block unless it consumes the entire path.
Trailing slashes will be ignored.

    :::ruby
    Renee {
      whole_path('test') {
        halt 'i got /test!' # your path must be exactly /test to get here.
      }
    }

### `#part`

This is similar to `#path`, except it's used for parts of the path instead of a whole part delimited by `/`'s. For example:

    :::ruby
    Renee {
      part('one') {
        part('two') {
          part('three') {
            halt "onetwothree" # this will only match /onetwothree
          }
        }
      }
    }

### `#extension`

You can also use `extension` as a way to define formats:

    :::ruby
    path '/test' do
      extension 'html' do
        halt 'html'
      end
      extension 'json' do
        halt 'json'
      end
    end

This will have `test.html` respond with 'html' and `test.json` respond with 'json'.

### `#var` (also `#variable`)

This will match a part of path (delimited by `/`'s) and yield that value back to the block passed to it. Example:

    :::ruby
    Renee {
      var { |id|
        # this will match /one and return "the id is `one'"
        halt "the id is `#{id}'"
      }
    }

You can access the variables (passed into the request) using the local variables yielded to the block. Variables are a powerful
way to express expected parameters for a given set of requests. You can specify variables that match a regex:

    :::ruby
    path('blog') {
      var(/\d+/) { |id| get { halt "path is /blog/#{id}" } }
    }

and even explicitly cast your variable types:

    :::ruby
    path('blog') {
      var :type => Integer do |id|
        get { halt "path is /blog/#{id} and id is an integer" }
      end
    end

### `#part_var` (also `#partial_variable`)

This will match a part of path and yield that value back to the block passed to it.
It will consume partial parts of the path like `#part`.

### `#remainder`

This will consume the remaining part of the path and yield that value to the block passed to it. Example:

    :::ruby
    Renee {
      remainder {|p|
        # this will match anything and yield the result.
        halt "I got #{p} back."
      }
    }

Notice this allows you to handle the cases within a particular route scope and manage them based on the "rest" of the uri yielded in the `remainder` block. You
can handle different remainders in all the different path blocks.

### `#complete`

This will only match if the `PATH_INFO` has been entirely consumed. If the PATH_INFO was only `/`, it
will be consumed by this method. Example:

    :::ruby
    Renee {
      complete {
        halt "consumption is complete" # this will only match '/' and ''
      }
    }

## Request method-based

Request methods can be defined easily:

    :::ruby
    run Renee {
      get    { halt "a get!"  }
      post   { halt "a post!" }
      put    { halt "a put!"  }
      delete { halt "a delete!" }
    }

### `#get`

This will match if the path is complete and the request method is `GET`. Optionally,
you can pass it a path, which will match using `complete_path` and then perform the request method matching.

### `#put`

This is the same as `#get`, except for the request method `PUT`.

### `#post`

This is the same as `#get`, except for the request method `POST`.

### `#delete`

This is the same as `#get`, except for the request method `DELETE`.

## Query-based

### `#query`

Query allows you either match a hash of key-value pairs from the query path.
Or, it will match a list of keys. If one of the keys is not present, this this will not yield
the values corresponding to the keys supplied.

Example:

    :::ruby
    Renee {
      query(:key => 'value') {
        halt "The query string contained key=value"
      }

      query :key, :secret { |key_value, secret_value|
        halt "Key is #{key_value}, secret_value is
          #{secret_value} (and these will never be nil)"
      }
    }

### `#query_string`

This will match an exact query string. It's equivalent to `match === env['QUERY_STRING']`
where match is the value passed in.

    :::ruby
    path 'foo' do
      query_string 'bar' do
        get { halt 'BAR!' }
      end

      query_string 'baz' do
        get { halt 'BAZ!' }
      end
    end

This will respond to `/foo?bar` with "BAR!" and `/foo?baz` with "BAZ!".
You can also specify query_string in a variety of other ways:

    :::ruby
    # Check key and value of query param
    query_string 'foo=bar' do
      post { halt [200,{},'foo'] }
    end

    # Declare query params as a hash
    query :foo => "bar" do
      halt 200
    end

    # Switch based on a query parameter
    query :foo do |var|
      case var
      when 'bar' then halt 200
      when 'bar2' then halt 500
      end
    end