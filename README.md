# jeditable\_wysiwyg\_rails

A gem to add in-place-editable fields to your Rails project. This gem provides assets for the [Jeditable](http://www.appelsiini.net/projects/jeditable) jQuery plugin, so most options for `$.editable()` can be passed through the `options` parameter of the helpers. Additionally, the [markItUp](http://markitup.jaysalvat.com/home/) and [jWYSIWYG]() jQuery plugins are included, together with the code which allows Jeditable to use either of those editors as custom inputs.

This gem is derived from the [`jeditable-rails`](https://github.com/afeld/jeditable-rails) gem, in particular inheriting its helper almost unchanged, so it should be possible to use this as a drop-in replacement for `jeditable-rails`.

## Requirements

* [jQuery](http://www.jquery.com) 1.2.6+ - see [jquery-rails](http://github.com/indirect/jquery-rails) for easy integration with your Rails project

## Installation

Assuming a Rails 3 project, in your Gemfile, add:

    gem 'jeditable_wysiwyg_rails', :git => 'git://github.com/pjmorse/jeditable-wysiwyg-rails.git'

Then add this to your application's `app/assets/javascripts/application.js` file:

    //= require jquery
    //= require jquery_ujs
    //= require jquery.jeditable

The first two lines may already be present; those scripts are provided by `jquery-rails`, which you should have installed.

If you plan to use jWYSIWYG, also add these lines:

    //= require jquery.wysiwyg
    //= require jquery.jeditable.wysiwyg

You will also need to add this to your `app/assets/stylesheets/application.css` file to include jWYSIWYG's CSS:

    *= require jquery.wysiwyg

If you plan to use markItUp, add these lines:

    //= require jquery.markitup
    //= require jquery.jeditable.markitup

## Usage

Suppose you have a `User` model with a `#zip_code` attribute.

```erb
<%# app/views/users/show.html.erb %>
<h1>View/Edit your profile</h1>
...
<b>Zip Code:</b>
<%= editable_field @user, :zip_code %>
```

The helpers can be used in any view you like.  The field appears as normal text to the user, but when clicked, turns into a form field that will then be submitted back to the server.  By default, jeditable_wysiwyg_rails requires an `update` action when a PUT is done to `url_for(object)`, which is generated by default for Rails scaffolds.

Jeditable also requires the server to respond with the new value.  For a simple `update` action, the code will look something like this:

```ruby
# app/controllers/users_controller.rb
def update
  # update your model
  @user = User.find(params[:id])
  @user.update_attributes!(params[:user])

  format.html {
    if request.xhr?
      # *** repond with the new value ***
      render :text => params[:user].values.first
    else
      redirect_to(@user, :notice => 'User was successfully updated.')
    end
  }
end
```

To add a textarea with the jWYSIWYG editor, say for the `#biography` attribute of the `User` model, you'll include additional parameters to the `editable_field` helper:

```erb
<%# app/views/users/show.html.erb %>
<h1>View/Edit your profile</h1>
...
<b>Biography:</b>
<%= editable_field @user, :biography %>
<%= editable_field @user, :biography, {  :type => 'wysiwyg',
                                         :rows => 10,
                                         :cols => 80,
                                         :submit => 'OK',
                                         :onblur => 'ignore',
                                         :wysiwyg => { :debug => false }
                                       } %>
```

N.B. the `rows` and `cols` options are vital to making sure Jeditable does not present a `textarea` of such small size as to be unusable!

For a complete list of parameters Jeditable accepts, [see its documentation](http://www.appelsiini.net/projects/jeditable). In this example, the `wysiwyg` parameter contains the options to be passed along to jWYSIWYG; it should not be empty or missing.

## Copyright

Parts of this gem are inherited from `jeditable-rails` which is:

Copyright (c) 2011 Aidan Feldman. See LICENSE.txt for
further details.
