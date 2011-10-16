# Route generation

To register paths for later generation, you have four methods at your disposal: `#register`, `#prefix`, `#path`, and `#url`.

## `#register`

This allows you to register a route for later generation:

    :::ruby
    app = Renee.new
    app.register(:path, '/path')
    app.path(:path) # Would return '/path'

This also allows for named variables in the path:

    :::ruby
    app = Renee.new
    app.register(:path, '/path/:id')
    app.path(:path, 123)        # Would return '/path/123'
    app.path(:path, :id => 123) # This would also return '/path/123'

If you have default values for variables, you can pass them in on the `#register` call:

    :::ruby
    app = Renee.new
    app.register(:blog_get, '/blog/:page', :page => 1)
    app.path(:blog_get)    # Would return '/blog/1'
    app.path(:blog_get, 5) # Would return '/blog/5'

## `#prefix`

This allows you to define common parts for nicer re-use:

    :::ruby
    app = Renee.new
    app.prefix("http://mydomain.com") {
      app.register(:test, "/test")
      app.register(:test2, "/test2")
    }
    app.url(:test) # Would return http://mydomain.com/test
    app.url(:test2) # Would return http://mydomain.com/test2

You can even store the objects created by `#prefix` and use them later. In the above example, this would also be equivalent:

    :::ruby
    app = Renee.new
    domain_prefix = app.prefix("http://mydomain.com")
    domain_prefix.register(:test, "/test")
    domain_prefix.register(:test, "/test2")

This makes registering similar routes more concise.

## `#path`

This creates paths for the paths you have registered. The first parameter is the name of the route. After that an optional hash can be
supplied to dictate the value of variables in the path. Any key-values not consumed by the path will be appended as a query string to
the generated path.

Example:

    :::ruby
    app = Renee.new
    app.register(:blog, '/blog')
    app.path(:blog)    # Would return '/blog'
    app.path(:blog, :page => 1) # Would return '/blog?page=1'

## `#url`

This is identical to path with one special consideration. If the registered path had a scheme, host or port, it will be added to the generated path:

    :::ruby
    app = Renee.new
    app.register(:blog, 'http://localhost/blog')
    app.url(:blog)    # Would return 'http://localhost/blog'

If you leave off the scheme, this will create scheme in-variant URLs:

    :::ruby
    app = Renee.new
    app.register(:blog, '://localhost/blog')
    app.url(:blog)    # Would return '://localhost/blog',
                      # did I mention, these are awesome?
