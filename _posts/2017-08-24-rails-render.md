---
title: What Exactly Does Rails’ `render` Do?
teaser: An in-depth look at what it does under the hood (particularly in controllers).
category: fundamentals
tags: [rails]
---

Whenever I want to dig deeper into how Rails works, the official documentation is always the first place I go (search query: `site:api.rubyonrails.org %s`). It’s remarkably comprehensive, so I was surprised to discover that information about the `render` method is [actually quite fragmented][search]. This post is an attempt to supplement the official documentation with a general introduction to the `render` method.

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

    def index
      @books = Book.all
    end

is the same as

    def index
      @books = Book.all
      render :index
    end

**`render :index`** says, ‘combine the data I've prepared (`@books = Book.all`) and the `books/index.html.erb` view template to generate a complete HTML document, then send that back to the client.’

**`redirect_to @book`** says, ‘tell the client to start the process over again, issuing a new `GET` request to `url_for(@book)`.

If you omit both, the action will render a template with the same name as the action itself. You only need to call `render` explicitly when the view template you want doesn’t match the action you’re rendering it from. Thus, the boilerplate

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

will redirect to a new URL (whatever is routed to `books#show` for the new record) on success, or stay on the `books#create` URL and return the `books/new.html.erb` view on failure.

> #### Comprehension Check
> 
> What happens in the following case?
> 
> 1. `books#create` and `books#new` are routed to two different paths,
> 2. you try to submit the ‘Create Book’ form on the New Books page,
> 3. the submission fails, and
> 4. you refresh the page.
> 
> Do you see a New Book form again? Which URL is your browser on?[^1]

Note that the `#create` action never tries to render its own view. [As the documentation states][render_docs] (emphasis mine),

> These two methods [`render` and `redirect_to`] represent the two basic action archetypes used in Action Controllers: **Get-and-show** and **do-and-redirect**. Most actions are variations on these themes.

#### Rendering views from other controllers

The usage outlined above only works for view templates that belong to the originating controller. To render view from other controllers, pass the `:template` option instead:

```ruby
render template: 'authors/new'
```

### In view templates

On the other hand, `render` does something totally different in view templates: it renders **partials**. For this use case, [the official documentation is more than ample][partials], but the upshot is this:

* You can create **partial view templates** to insert into your standard templates (think of them as page components).
* Filenames of partials must begin with an underscore (_e.g.,_ `_nav.html.erb`).
* Use `render 'nav'` if you want to include the `_nav.html.erb` partial **from a view located in the same folder**.
* Use `render 'shared/nav'` if you want to include the `app/views/shared/_nav.html.erb` partial **from any view in your project**.
* Various options and shorthand syntaxes exist for passing data into a partial, rendering multiple partials from a collection, and more.

### In review

| `render _____`         |  Used in     | Converts | ...from         | ...into
| ---:                   |  :---        | :---     | :---            | :---
| `:index`               |  Controllers | Views    | same controller | the HTML document in an HTTP response
| `template: 'post/new'` |  Controllers | Views    | any controller  | the HTML document in an HTTP response
| `'flash'`              |  Views       | Partials | same folder     | a page component in a larger view template
| `'shared/flash'`       |  Views       | Partials | any folder      | a page component in a larger view template

---

[^1]:

    After Step 3 (when the submission failed), your browser would render all the same content you would normally see at the URL for `books#new`, but it would actually be pointing to the URL for `books#create`. It would have gotten this content as the result of a `POST` request, so when you refresh, it should attempt to submit the same `POST` request again, prompting you to confirm that you want to resend the data first.
    
    Or at least, that’s how it should work in principle. In my own cursory testing, it actually submits a `GET` request to the `books#create` URL upon refresh, which results in a Rails routing error (`No route matches [GET] "/books/create"`). If anyone has any insight on this matter, I’d be glad to hear it.

[search]: https://www.google.com/search?q=site:api.rubyonrails.org+render
[render_docs]: http://api.rubyonrails.org/classes/ActionController/Base.html
[partials]: http://api.rubyonrails.org/classes/ActionView/PartialRenderer.html
