# Helper Methods Part 1: Named routes and links

## Getting started

There is no target for this app, this is just a playground to repeat and levelup what we've learned so far using industry standard (i.e. Professional) code.

This project does include a few basic automated tests, so click on this button to get started:

LTI{Load Helper Methods Part 1 and 2 assignment}(https://grades.firstdraft.com/launch)[S9ymPy6WCsn18gLbByVbZQ7k]{vfdtzJb5bLYqYwuqgeRKpc5d}(10)[Helper Methods Part 1 and 2 Project]

The staring point code for `helper-methods-part-1-and-2` is where you should have arrived by the end of the previous `getting-started-with-scaffolds` project. The current project `helper-methods-part-1-and-2` covers everything in this lesson and the next lesson _Helper Methods Part 2_, so you will need to keep it open when you move onto that lesson in order to build on your progress in this one.

The third lesson in the series, _Helper Methods Part 3_, has a separate project link that you will access in that lesson.

## Refactoring with automated tests 

Before we go further, let's touch on our automated tests, or "specs".

From here on, we are not writing as many automated tests for you; only a few basic ones to check your progress with `rake grade`. Soon you will be writing your own tests. For now, the simple tests we wrote should be sufficient for the refactoring we will be doing through this project. 

The `rake grade` specs are _already all passing_. As we refactor for the rest of the project, you should run the tests periodically to ensure you didn’t break anything.

Take a moment and examine one of test blocks in the `spec/features/1_basic_spec.rb` file:

```ruby
# ...
describe "The /movies/new page" do
  # ...
  it "creates a movie successfully", point: 1 do
    visit "/movies/new"

    fill_in "Title", with: "My test movie"
    fill_in "Description", with: "description"
    click_button "Create Movie"

    expect(page).to have_content("Movie created successfully."),
      "Expected to fill in the new movie form, click 'Create Movie', and be redirected to the movie index with a success notice"
  end
# ...
```

We `describe` a URL path that we want to test, in this case `/movies/new`. Then we run a test that checks that `it "creates a movies successfully"`. This test block begins by `visit`ing the URL path, then the test `fill`(s)`_in` the "Title" and "Description" and `click`(s the)`_button` "Create Movie". Finally, the test `expect`s the ending `page` to `have`(the)`_content`: "Movie created successfully" and fill fail if this is not found!

<aside markdown="1">
Our specs are written with the [RSpec testing framework gem](https://rspec.info/). There is also the Minitest framework that comes out of the box with Rails ([see the official guide](https://guides.rubyonrails.org/testing.html)). We wrote our tests with RSpec because it is somewhat more flexible and allows for specific things we do to make `rake grade` provide nicer feedback for learners.
</aside>

Just like you would do by manually clicking through the page to check your work, but the automated test does it all for you, and you can rerun it quickly anytime you make a change to the code. Yay!

## Walkthrough video

For the rest of this lesson, there is a walkthrough video available.

<div class="bg-red-100 py-1 px-5" markdown="1">
**Please note**, the video is from a previous iteration of the project, so there are some differences:

- The project was previously called `helper-methods`, but it has been renamed to `helper-methods-part-1-and-2`
- I am using Gitpod as my cloud editor, so the interface looks a bit different.
- Anything contained in the project "README" is now contained in this Lesson
- I use a graphical user interface at the URL path `/git` to commit and push, _you_ should use [the VSCode built in workflow in this lesson](https://learn.firstdraft.com/lessons/50-git-commit-and-push)
- I use `bin/server` to start my live app preview, _you_ should use `bin/dev`
- You should drop the `.html.erb` when rendering a view template:
    `render({ :template => "photo_templates/index" })` 
    _instead of_ 
    `render({ :template => "photo_templates/index.html.erb" })`
</div>

Did you read the differences above? Good! Then [here is a walkthrough video for this project.](https://share.descript.com/view/bY2qZcJtJHl)

**As you watch the video, pause it frequently, read the associated text, and type out the code.**

## Cleaning up `config/routes.rb`

Let's head over to our routes:

```ruby
# config/routes.rb

Rails.application.routes.draw do
  get("/", { :controller => "movies", :action => "index" })

  # Routes for the Movie resource:

  # CREATE
  post("/movies", { :controller => "movies", :action => "create" })
  get("/movies/new", { :controller => "movies", :action => "new" })
          
  # READ
  get("/movies", { :controller => "movies", :action => "index" })
  get("/movies/:id", { :controller => "movies", :action => "show" })
  
  # UPDATE
  patch("/movies/:id", { :controller => "movies", :action => "update" })
  get("/movies/:id/edit", { :controller => "movies", :action => "edit" })
  
  # DELETE
  delete("/movies/:id", { :controller => "movies", :action => "destroy" })

  #------------------------------
end
```

This looks familiar from our getting started lesson. It's basically where we left off. We can see the different HTTP verbs and our RESTfully named routes. But, right away we see there's optional syntaxes that we need to change. 

 - Anywhere you see the old `Hash` syntax (`:symbol` keys with hash rockets `=>`): switch to [the new `Hash` syntax](https://learn.firstdraft.com/lessons/116-optional-syntaxes-in-ruby#new-hash-syntax).
 - Anywhere you see [optional curly braces around `Hash` arguments](https://learn.firstdraft.com/lessons/116-optional-syntaxes-in-ruby#new-hash-syntax#curly-braces-around-hash-arguments): remove them.
 - _Optionally_ drop the parentheses around arguments.

The last point is optional since it's not something that I always do. I like the parentheses around arguments to a method to make it clear to me that _these_ are the arguments that go with _this_ method: `method(argument_one, argument_two)`.

Especially if there's an order of operations with multiple method calls on the same line of code. My convention is: if there's no other methods on this line. There's no order of operations concerns. I will leave out the parentheses.

Type those changes out by hand to get some muscle memory!

---

We can actually go further with this shortening. Rather than saying 

`get "/movies", controller: "movies", action: "create"` 

when writing routes, we can replace that whole thing as 

`get "/movies" => "movies#create"`. 

All we are doing here is passing a hash argument to the `get` method, and this hash is `{ "/movies" => "movies#create" }` (with the curly braces dropped because it is the last argument to the method).

Remember, the `#` symbol is used to indicate an `Object#method`, which, in this example, is `MoviesController#create`. 

We can go also go shorter for the homepage route at the URL path `/`. We can just write: `root "movies#index"`. `root` is a method provided by Rails to define the "homepage". It doesn't need a URL path, because the homepage is always `/`.

Do this refactoring on your own for all of the routes.

---

Just imagine how much typing it would've saved us if we were doing this from the very beginning! But, as beginners, it would've been very confusing. 

This is going to be the pattern of this lesson. We're going to start writing the very abbreviated versions of everything. That's the kind of code you're going to be reading out in the world.

Click around in the app (or better yet, run some automated tests with `rake grade`). Everything should be working exactly as before after this refactoring. This is a perfect time for git commit!

## Route helper methods

At the bash prompt, run `rails routes`:

```
    Prefix Verb   URI Pattern                                Controller#Action
            GET    /                                         movies#index
    movies POST   /movies(.:format)                          movies#create
movies_new GET    /movies/new(.:format)                      movies#new
            GET    /movies(.:format)                         movies#index
            GET    /movies/:id(.:format)                     movies#show
            PATCH  /movies/:id(.:format)                     movies#update
            GET    /movies/:id/edit(.:format)                movies#edit
            DELETE /movies/:id(.:format)                     movies#destroy
```

There's a whole bunch in here. You may need to make your terminal wider to prevent line wrapping, since it's trying to print a big table with the columns `Prefix`, `Verb`, `URI Pattern`, and `Controller#Action`. We just want to focus on the top of the table.

What do those column names mean to you? Do the values in each row make sense? 

Hopefully they do now! We have our different HTTP verbs, the paths we defined in our routes, and the controller and action related to each route. 

<aside markdown="1">
`(.:format)` at the end of the "URI Pattern" column, allows our app to respond to multiple formats, like `.html` and `.json` (if we wanted to build an API endpoint).
</aside>

What about the `Prefix`? 

Well, we just saw that Rails comes with the method `root` that always defines the `/` homepage route. `Prefix` means that Rails has defined a method that we can call _anywhere_ in our view templates, controller actions, etc., that will return the path as a string. And look, there's some other methods defined in the `Prefix` column: `movies` and `movies_new`.

You can also view all of this route information in your browser by navigating your live app to `/rails/info/routes`, and you will see a searchable table, with the actual full names (not just the "prefix") of the helper methods (e.g. `root` is actually `root_path`):

Open `app/views/movies/index.html.erb` and add some embedded Ruby:

```erb{9-11}
<!-- app/views/movies/index.html.erb -->

<h1>
  List of all movies
</h1>

<hr>

<div>
  <%= movies_path %>
</div>
```

Now refresh the `/movies` page in the live app to see what that embedded Ruby renders. What do you know, it rendered the string `"/movies"`! 

What if we change the code to `<%= movies_new_path %>`? Refresh `/movies` after this change, and it renders `"/movies/new"`. Following this pattern, `<%= root_path %>` just renders `"/"`.

`movies` and `movies_new` are static routes, without dynamic ID numbers in the path, so Rails just puts the segments together and gives you the method. But, for all the rest of the routes that do contain `:id`, there are no methods defined yet. We get to define those methods ourselves!

Back in `config/routes.rb`, add some code to give the movie details page its own method:

```ruby{5:(37-51)}
# config/routes.rb

# ...
  get "/movies" => "movies#index"
  get "/movies/:id" => "movies#show", as: :details
  # ...
end
```

With the `as:` option, I got to pick a method name (in the form of a symbol). And back on `/rails/info/routes` we should see our new route.

And we can check in our `app/views/movies/index.html.erb` view template by changing the embedded Ruby to: `<%= details_path %>`, and on the live app we should see... an error!

```
No route matches {:action=>"show", :controller=>"movies"}, missing required keys: {:id}
```

That's because there is a flexible (a.k.a. dynamic) segment in the route, so Rails can't produce the path unless you tell it what to put in that spot!

Change the embedded Ruby to `<%= details_path(42) %>`. Refresh `/movies`. It should now render the string `"/movies/42"`.

There's a couple of benefits to these route **helper methods**. 

One, is that there is actually two versions of the method, `_path` and `_url`. If you change the method in the view template to `details_url(42)` and refresh `/movies`, then you will see the full URL of the app, including the path! 

This is very cool, because it means I can use fully qualified URLs. And when I deploy my code, and it's running on `mydomain.com`, this method will automatically work -- no matter what server it's running on! No need to hard code anything, and then later need to change it when I move my app around.

The biggest benefit of using the route helper methods is the following: If you name your routes once in your `config/routes.rb`, and outside of this file in your application, _never_ refer to paths or URLs and only refer to the methods associated with them, then if you ever have to change the URL from `movies` to `films` (or `zebra` or anything!), you don't need to do this throughout your codebase and risk breaking everything.

With our new route helper methods, we're never going to refer to these paths as actual strings anymore. We're always going to refer to them through these method names that we give them. 

Now let's get these names down per the conventions that most Rails developers use:

```ruby{7,8,12,16}
# config/routes.rb

Rails.application.routes.draw do
  root "movies#index"

  # CREATE
  post "/movies" => "movies#create", as: :movies
  get "/movies/new" => "movies#new", as: :new_movie
          
  # READ
  get "/movies" => "movies#index"
  get "/movies/:id" => "movies#show", as: :movie
  
  # UPDATE
  patch "/movies/:id" => "movies#update"
  get "/movies/:id/edit" => "movies#edit", as: :edit_movie
  
  # DELETE
  delete "/movies/:id" => "movies#destroy"

  #------------------------------
end
```

We don't need to define methods twice for paths that already have a method defined. These methods just return a string with the path name, they ignore the HTTP verb, so there's no difference between naming a route helper method for `delete` or `patch` above; both will return `"/movies/ID"`. We defined the method `movie_path` and `movie_url` for all three `get`, `patch`, and `delete` HTTP verbs for this route just by adding it once.

That's it! We've now provided names for all of our routes. Now we want to use these every single place in our application where we are using string paths.

There's one I see right away in the `app/views/movies/index.html.erb` we were using to demonstrate the helper methods:

```erb{5}
<!-- app/views/movies/index.html.erb -->

<!-- ... -->
<div>
  <a href="/movies/new">Add a new movie</a>
</div>
<!-- ... -->
```

That should be:

```erb{5}
<!-- app/views/movies/index.html.erb -->

<!-- ... -->
<div>
  <a href="<%= new_movie_path %>">Add a new movie</a>
</div>
<!-- ... -->
```

Refresh `/movies` in the live app. If all went well, there should be no error messages and if you "view source" on the page, you won't see any Ruby, just the string that the method returned: `"/movies/new"`. Same as before!

This is a pattern that should be repeated throughout the day. Use view source constantly to confirm that the handwritten HTML that we did before matches up with the HTML that these helper methods are producing for us. Then we know that we're using them correctly. 

Time for a git commit after all that preparation!

## Cleaning up the routes

Now that we gave names to our routes, we're going to go through and find every reference to a URL anywhere in our application and replace it.

For instance, at the bottom the `<table>` in the `index` view template:

```erb{5}
<!-- app/views/movies/index.html.erb -->

<!-- ... -->
    <td>
      <a href="/movies/<%= a_movie.id %>">
        Show details
      </a>
    </td>
<!-- ... -->
```

Should be:

```erb
      <a href="<%= movie_path(a_movie.id) %>">
```

We supply the movie ID as an argument to our new method. And if we try and refresh our live app and click one of the "Show details" links on the `/movies` page, it should work. We can also "view source" and see the HTML we expect in that link location: `"/movies/ID"`. Great!

Now on to the `show` template, there's three links near the top to change:

```erb{6,12,18}
<!-- app/views/movies/show.html.erb -->

<!-- ... -->
    <div>
      <div>
        <a href="/movies">
          Go back
        </a>
      </div>

      <div>
        <a href="/movies/<%= @the_movie.id %>/edit">
          Edit movie
        </a>
      </div>

      <div>
        <a href="/movies/<%= @the_movie.id %>" data-turbo-method="delete">
          Delete movie
        </a>
      </div>
    </div>
<!-- ... -->
```

Those index, edit, and delete links should be using the helper methods:

```erb{6,12,18}
<!-- app/views/movies/show.html.erb -->

<!-- ... -->
    <div>
      <div>
        <a href="<%= movies_path %>">
          Go back
        </a>
      </div>

      <div>
        <a href="<%= edit_movie_path(@the_movie.id) %>">
          Edit movie
        </a>
      </div>

      <div>
        <a href="<%= movie_path(@the_movie.id) %>" data-turbo-method="delete">
          Delete movie
        </a>
      </div>
    </div>
<!-- ... -->
```

Again, we supply arguments to the helper methods that need an ID number.

Test those links out manually to be sure everything worked.

Make the last two changes on the `new` and `edit` pages in the form `action=""` attribute.

---

Now we've seen that there's only one name (`movie_path(id)`) for a single movie resource, and then there's three actions that modify that single movie resource with different HTTP verbs: `get` to show details, `patch` to edit, and `delete` to destroy. 

Now here's something cool. Wherever we wrote:

```ruby
movie_path(@the_movie.id)
```

We can be a bit more concise and omit the `.id`:

```ruby
movie_path(@the_movie)
```

If we just give `movie_path` an instance of `ActiveRecord`, then Rails will figure out the ID number for us by searching that record for the `id` column. Go back through the view templates and remove the `.id` for that helper method.

Now we need to clean up the routes in our `app/controllers/movies_controller.rb` file, since we were using them a lot for redirects. For instance, in the `create` action:

```ruby{12}
# app/controllers/movies_controller.rb

class MoviesController < ApplicationController
  # ...
  def create
    @the_movie = Movie.new
    @the_movie.title = params.fetch("query_title")
    @the_movie.description = params.fetch("query_description")

    if @the_movie.valid?
      @the_movie.save
      redirect_to(movies_url, { :notice => "Movie created successfully." })
    else
      render template: "movies/new"
    end
  end
  # ...
```

Wait! Why did we change `"/movies"` in that `redirect_to` to `movies_url`, rather than the `movies_path` we've been using? Technically we should only use paths on the client facing view templates. But, when we're sending responses back, we should use the fully qualified URL, including the domain name.

Can you find the other places in the code where we need to change the path to the `_url` helper method? (Hint: look for any `redirect_to`s, and remember `movie_url` takes an argument of the `ActiveRecord` instance with or without the `.id`.) 

While we're at it, in the `movies_controller.rb` file, try to go through and change all of the old syntax to our new syntax. For instance, in the `create` action, this:

```ruby
redirect_to(movies_url, { :notice => "Movie created successfully." })
```

Should be:

```ruby
redirect_to movies_url, notice: "Movie created successfully."
```

We won't need to go back and change things like this going forward, because we'll always be writing code from the get go with our new syntax.

Once you've changed all the syntax, and confirmed you didn't break anything by clicking around in the live app or by running some automated tests with `rake grade`, then make a git commit.

## Shorten template names

Let's do another thing to make our code more professional. Whenever we render a template in our `movies` controller, we were writing out the whole name:

```ruby{7}
# app/controller/movies_controller.rb

  # ...
  def new
    @the_movie = Movie.new

    render template: "movies/new"
  end
  # ...
```

We don't actually need `template:` because if you pass the `render` method a single string argument, it assumes that is a view template!

```ruby{4}
  def new
    @the_movie = Movie.new

    render "movies/new"
  end
```

Next, if the folder name that the view templates are located inside of matches the name of the controller, and if the action name matches the name of the template, we can just get rid of the whole string! (And the method!)

```ruby
  def new
    @the_movie = Movie.new
  end
```

Wow! Rails looks at the controller `MoviesController` and assumes the view template is in `app/views/movies`, and then looks at the action `new` and assumes the template is named `new.html.erb`, and then it assumes you want to render that template given that action (which we do).

Try and go through the code in the controller and delete any render statements _where the template matches the action name_ (only when it matches, Rails won't figure out when it doesn't).

Once you finish that refactoring, make sure your app still works, and then git commit.

## `link_to` helper method

Next we're going to talk about a very, very useful helper method called `link_to`, that will replace all of our plain HTML `<a>` link elements. 

This is the first example of a helper method that produces an actual HTML element. 

Let's experiment with a simple example with some dummy code in our `app/views/movies/index.html.erb` view template:

```erb
<%= link_to "Go to Google", "https://www.google.com" %>
```

We have the method (`link_to`) and two arguments (remember, we omitted the parentheses from `link_to()`): Some text to display ("Go to Google"), and the URL (google). 

Refresh `/movies` in the live app, and you will see a working link. And if you view source on the page, you should see:

```erb
<a href="https://www.google.com">Go to Google</a>
```

Just as we would have written in plain HTML, but now we're letting Rails do that for us. 

Let's see how that benefits our code by changing the "Add a new movie" link (we can remove our Google link):

```erb
  <!-- <a href="<%= new_movie_path %>">Add a new movie</a> -->

  <%= link_to "Add a new movie", new_movie_path %>
```

Try out the new link and view source on it. Beautiful. So now we have a nice link that doesn't rely on us writing any HTML, just embedded Ruby.

Let's look at a really cool benefit from doing this replacement. Look at the "Show details" link:

```erb
<%= link_to "Show details", movie_path(a_movie) %>
```

We have our helper method, the text to display, and the path to link to. Now here's where people start to refer to the "Rails Magic".

What if I provide the second argument not as the `movie_path(a_movie)`, which returns the string `"/movie/ID"`, but rather just the `ActiveRecord` object _itself_:

```erb
<%= link_to "Show details", a_movie %>
```

This will work, and produce the correct link! Try it in the live app.

What's happening here? Secretly, `link_to` checks and says, what `.class` is `a_movie`? In this case, that would return `Movie`. Then it does a `.downcase`. Then it does a `+ "_url"` on that, and then it calls that helper method: 

```ruby
a_movie.class.downcase + "_url"
```

So `link_to` looks for a named route helper method that matches the name of `a_movie`'s class (`Movie`), because that's the _convention_. If you have a table called `movies`, you're going to have RESTful routes with names like `"/movies"` and `"/movie/ID"`. If you're doing everything by _convention_, this shorthand works!

Take a moment now to go through and replace `<a>` elements with our `link_to` embedded Ruby in the view templates: `index.html.erb`, `show.html.erb`, `new.html.erb`, and `edit.html.erb`.

<div class="bg-red-100 py-1 px-5 bleed-full" markdown="1">

Your delete link should end up like:

```erb
<%= link_to "Delete Movie", @movie, data: { turbo_method: :delete } %>
```

with the `data: { turbo_method: :delete }` argument.
</div>

Remember to `rake grade` and git commit when you are done.

___
