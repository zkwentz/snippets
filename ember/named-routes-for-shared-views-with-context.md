###Named Routes for Shared Views with Context

Assuming you want a shared partial for all of your pages, at the application template level, but you want the properties to reference the current controller.

First, create a named outlet in your template:

**application.emblem**
```haml
== outlet 'header'

== outlet
```

Then, a mixin, for the routes that you want to assume control of the named outlet:

**app/mixins/routes/header-route-mixin.coffee**
```coffee
HeaderRouteMixin = Ember.Mixin.create(
  renderTemplate: (controller, model) ->
    @render('layouts/partials/header',
      outlet: 'header'
      into: 'application'
      controller: controller
      model: model
    )
    @_super(controller,model)
)
```

Include that mixin in the routes you want to assume control:

**app/routes/posts-route.coffee**
```coffee
PostsRoute = Ember.Route.extend(
  HeaderRouteMixin,
  ...
)
```

**app/routes/application-route.coffee**
```coffee
ApplicationRoute = Ember.Route.extend(
  HeaderRouteMixin,
  ...
)
```

Now, when each new route renders its own template, it will also render into the
(header/footer/sidebar) partial with it's own context or scope.
