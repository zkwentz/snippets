###Controller Partial w/ Multiple Contexts

Assuming you want a shared partial for all of your pages, at the application template level, but you want the properties to reference the current controller.

First, create a mixin, that sets the controller you want to reference the properties on, to your `application` controller:

```coffee
CurrentControllerControllerMixin = Ember.Mixin.create(
  needs: ['application']
  _currentController: Ember.computed.alias('controllers.application.currentController')

  isActiveObserver: (->
    @set('_currentController',@)
  ).observes('isActive').on('init')

)
```

*Note: Using my addon, [ember-cli-controller-isactive](https://www.npmjs.com/package/ember-cli-controller-isactive), as a dependency here.*

In your `application` controller, set the property to be itself by default:

```coffee
ApplicationController = Ember.Controller.extend(
  ...
  currentController: @
  ...
)
```

So in your application template you can do the following:

```haml
render 'layouts/partials/header' currentController
```

*layouts/partials/-header.emblem*
```haml
nav
  a.brand-logo navigationTitle
  ul
    li
      ...
```


*Note: [.emblem](https://github.com/machty/emblem.js/) syntax*

And in controllers that you want to change the header scope:

```coffee
MyController = Ember.Controller.extend(
  CurrentControllerControllerMixin,

  fullNameObserver: (->
    @set('navigationTitle',@get('model.fullName'))
  ).observes('model.fullName')

  navigationTitle: ''

)
```

Originally, I had just set the properties themselves on the application controller, and left it as a partial, however this became really valuable when getting more complicated like adding links and things that sent actions, which the application controller wouldn't and shouldn't respond to.
