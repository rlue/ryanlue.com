---
title: What Exactly Does Rails’ `render` Do?
teaser: An in-depth look at what it does under the hood (particularly in controllers).
category: fundamentals
hidden: true
tags: [rails]
---

Whenever I want to dig deeper into how Rails works, the official documentation is always the first place I go (search query: `site:api.rubyonrails.org %s`). It’s remarkably comprehensive, so I was surprised to discover that information about the `render` method is [actually quite fragmented][search]{:target="_blank"}.

This is especially confusing because `render` is used both in controllers and in view templates, and serves related (but wholly different) purposes in each. While there are multiple pages describing what `render` does in different contexts, there is no single document outlining the overall scope of where and how it can be used. This post is an attempt to address this deficiency and cover the two main purposes of `render` in broad strokes.

### In controllers

Controller actions generally end with one of two method calls: `render` or `redirect_to`. To understand how they work, let's review what controllers do in a Rails app: 

* A user tries to access a page.  
  (http://localhost:3000/books/index.html)
* Under the hood, the browser sends an HTTP request for the specified path on the server.  
  (`GET /books/index.html`)
* The Rails routing system then looks up which controller corresponds to the given request path.  
  (`books GET /books/index(.:format)            books#index`)
* The controller prepares some data and then **tells the server what response (_i.e.,_ what HTTP header/body content) to send back to the client**.

This last step occurs explicitly when you call `render` or `redirect_to`, or **implicitly if you leave it out**.

That is, 

```ruby
def index
  @books = Book.all
end
```

is the same as

```ruby
def index
  @books = Book.all
  render :index
end
```

**`render :index`** says, ‘combine the data I've prepared (`@books = Book.all`) with the `books/index.html.erb` view template to generate a complete HTML document, then send that back to the client.’

**`redirect_to @book`** says, ‘tell the client to start the whole process over again, issuing a new `GET` request to `url_for(@book)`.

If you omit both, the action will render a template with the same name as the action itself. In other words, you only need to call `render` explicitly when the view template you want doesn’t match the action you’re rendering it from.

Thus, this controller boilerplate

```ruby
def create
  @book = Book.new(book_params)

  respond_to do |format|
    if @book.save
      format.html { redirect_to @book }
      format.json { render :show, status: :created, location: @book }
    else
      format.html { render :new }
      format.json { render json: @book.errors, status: :unprocessable_entity }
    end
  end
end
```

will redirect to a new URL (whatever is routed to `books#show` for the new `@book` record) on success, or stay on the `books#create` URL and return the `books/new.html.erb` view on failure.

> #### Comprehension Check
> 
> What happens in the following case?
> 
> 1. `books#create` and `books#new` are routed to two different paths,
> 2. you try to submit the ‘Create Book’ form on the New Books page,
> 3. the submission fails, and
> 4. you refresh the page.
> 
> Should the page render successfully? Which URL should you see in your
> browser’s address bar?
> 
> Follow this footnote for the answer.[^1]

Note that the `#create` action does not have a view of its own to render. [As the documentation states][render_docs]{:target="_blank"} (emphasis mine),

> These two methods [`render` and `redirect_to`] represent the two basic action archetypes used in Action Controllers: **Get-and-show** and **do-and-redirect**. Most actions are variations on these themes.

#### Rendering views from other controllers

The usage outlined above only works for view templates that belong to the originating controller. You can also render views from other controllers by passing the `:template` option instead:

```ruby
render template: 'authors/new'
```

although I’d be hard-pressed to think of a scenario where this is a good idea.

#### Implications for `flash`

The `flash` hash is a mechanism in Rails for persisting data (usu. to provide feedback in the UI) up until the completion of the next controller action. If you set a `flash` message in this controller action, it will be available to:

* the current view template,
* the next controller action, and
* the next controller action’s view template,

at which point it will be discarded.

Generally, the idea is for flash messages to appear only once. If your controller action ends with a `redirect_to`, then everything’s dandy — there is no current view template, so you’ll only see it on the next page.

But if you’re `render`ing a template, you’ll wind up seeing the flash message both on the current page and the next one. Hence, Rails provides `flash.now`, whose data is discarded at the end of the current controller action.

> #### Comprehension Check
> 
> Suppose you wanted to `render` the view in the current controller action, but
> also set a flash message that does not appear until the _next_ controller
> action (_i.e.,_ page load). How could you go about that?
> 
> Follow this footnote for the answer.[^2]

### In view templates

On the other hand, when used in view templates, `render` is for incorporating **partials**. For this use case, [the official documentation is more than ample][partials]{:target="_blank"}, but the upshot is this:

* You can create **partial view template** files to be inserted into your standard templates (think of them as modular page components).
* Filenames of partials must begin with an underscore (_e.g.,_ `_nav.html.erb`).
* Use `render 'nav'` if you want to include the `_nav.html.erb` partial **from a view located in the same folder**.
* Use `render 'shared/nav'` if you want to include the partial at `app/views/shared/_nav.html.erb` **from any view in your project**.
* Various options and shorthand syntaxes exist for passing data into a partial, rendering multiple partials from a collection object, and more.

### In review

In characteristic Rails style, `render` accepts a variety of argument types, and reads pretty naturally to the untrained eye (all things considered). But because it serves two unrelated purposes, it’s actually rather strict about what each argument type does (unlike other methods in Rails).

Argument to `render`   | Used in     | Renders  | ...from        
:---                   | :---        | :---     | :---           
`:index`               | Controllers | Views    | same controller
`template: 'post/new'` | Controllers | Views    | any controller 
`'flash'`              | Views       | Partials | same folder    
`'shared/flash'`       | Views       | Partials | any folder     

That’s the gist of it, anyway. For more details, as usual, the official documentation is the way to go — but at least now, you’ll know what part of it to scope out.

---

[^1]:

    After Step 3 (when the submission failed), your browser would render all the same content you would normally see at the URL for `books#new`, but it would actually be pointing to the URL for `books#create`. It would have gotten this content as the result of a `POST` request, so when you refresh, it should attempt to submit the same `POST` request again, prompting you to confirm that you want to resend the data first.
    
    Or at least, that’s how it should work in principle. In my own cursory testing, it actually submits a `GET` request to the `books#create` URL upon refresh, which results in a Rails routing error (`No route matches [GET] "/books/create"`). If anyone has any insight on this matter, I’d be glad to hear it.

[^2]:

    Simply make an explicit call to `render` _before_ setting the flash message:

    ```ruby
    def index
      render :index
      flash.notice = 'foo'
    end
    ```

[search]: https://www.google.com/search?q=site:api.rubyonrails.org+render
[render_docs]: http://api.rubyonrails.org/classes/ActionController/Base.html
[partials]: http://api.rubyonrails.org/classes/ActionView/PartialRenderer.html
