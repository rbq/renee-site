# Concept

Renee is a new Rack-based library for expressing web applications. Sinatra delivered a new simple way to think about
building web applications. The popularity of Sinatra both as a library and as a concept shows how enduring the
concept really has been.

Sinatra was different from Rails because the entire DSL was lightweight, easy to read and
combined routing and actions into a single file. However, let's consider an example from Sinatra to see where
we can improve upon this.

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
