# Settings

Configuration in Renee is simply using the `#setup` command to set
the appropriate options within a Renee application:

    :::ruby
    Renee { ... }.setup do
      views_path "./views"
      include Some::Module
    end

The available configuration options are:

 * `views_path`: The path to the templates within an application.
 * `include`: Register a module within an application.

More options are to come based on Renee configuration needs as they arise.