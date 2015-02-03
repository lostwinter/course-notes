# Rails [Tealeaf]
## Forms

These are my notes from week 2 of the Rapid Deployment course.

#### Prodedure
1. Create 'Post' resource. Use model-backed forms to create and edit post objects. Add a validation and display error on the template.
2. Extract `form_for` from `new` and `edit` as a partial.
3. Create a category model-backed form. Add route, controller#actions, views, and validations.
4. Extract an "Errors" partial for new category and new post forms. 
5. Add "New Comment" form on `show` post page. Nested routes, controller#create
6. Display categories associated to each post. Links to a "Category" page of notes.
7. Fix URLs and Date/Times with helpers.

[Solution App Link](http://tl-postit.herokuapp.com)

```
lostwinter@postit:~/workspace/postit-app (master) $ rake routes
       Prefix Verb  URI Pattern                        Controller#Action
         root GET   /                                  posts#index
post_comments POST  /posts/:post_id/comments(.:format) comments#create
        posts GET   /posts(.:format)                   posts#index
              POST  /posts(.:format)                   posts#create
     new_post GET   /posts/new(.:format)               posts#new
    edit_post GET   /posts/:id/edit(.:format)          posts#edit
         post GET   /posts/:id(.:format)               posts#show
              PATCH /posts/:id(.:format)               posts#update
              PUT   /posts/:id(.:format)               posts#update
```

HTTP   URI                    ACTION                   NAMED ROUTE
get    '/posts',              to: posts#index          posts_path
get    '/posts/object'        to: posts#show           post_path(object)
get    '/posts'               to: posts#new            new_post_path
get    '/posts/object/edit'   to: posts#edit           edit_post_path(object)
post   '/posts'               to: posts#create
patch  '/posts/object'        to: posts#update
put    '/posts/object'        
delete '/posts/object'        to: posts#destroy   


#### Strong Parameters
Create private method to handle strong parameters.
In controller file eg: posts_controller.rb

```ruby
private
def post_params
  params.require(:post).permit(:title, :url, :creator)
  # permit! to allow all.
end
```

#### Rails Model-Backed Forms
Model-backed forms are tied to objects. There has to be a setter method froom either a virtual attribute or column attribute.
A form is created around a specific model object like `@post`. 

```ruby
<%= form_for @post do |f| %>
  <%= f.label :title %>
  <%= f.text_field :title %>
  <br/>
  <%= f.label :url %>
  <%= f.text_field :url %>
  <br/>
  <%= f.submit "Create Post", class: 'btn btn-primary' %>
<% end %>
```
Has to be an attribute or virtual attribute of the object.
Check out the embedded hash structure in input id in html: name="post[url]"

Need to set up object in controller. 
```ruby
def new
  @post = Post.new
end
```
```ruby
def create
  @post = Post.new(post_params) # referring to a strong parameter

  if @post.save
    flash[:notice] = "Your post was created."
    redirect_to posts_path
  else
    render 'new'
end
```
Common usage: `Post.create(params[:post])`

##### Rails Form Helpers
```ruby
<%= form_tag '/posts' do %>
  <%= label_tag :title %>
  <%= text_field_tag :title, "Input a title" %>
  <br/>
  <%= submit_tag "Create Post", class: 'btn btn-primary' %>
<% end %>
```

You can use a non-model-backed form when form isn't tied to a model. 
#### Add Validations
Add to model layer eg. `post.rb`

`validates :title, presence: true`


#### Validation Errors
In console, if `post = Post.new(title: "")` is invalid, you may want to see error
`post.errors.full_messages` You can use this to show user validation errors

Add conditional to `new.html.erb`
```ruby
<% if @post.errors.any? %>
  <div class='alert alert-error'>
  <h5>There were some errors</h5>
    <ul>
      <% @post.errors.full_messages.each do |msg| %>
        <li><%= msg %></li>
      <% end %>
    </ul>
  </div>
<% end %>
```

Put the form in a partial since it's basically the same in new and edit.
`_form.html.erb`
Go to new.html.erb and simply add: ```ruby <%= render 'form' %>``` 

Set up edit controller 
```ruby
def edit
  @post = Post.find(params[:id])
end
```
Add layout `edit.html.erb`
Add links to access route `<% link_to 'edit', edit_post_path(post) %>

#### Update Action
```ruby
def update
  @post = Post.find(params[:id])

  if @post.update(post_params)
    flash[:notice] = "The post was updated"
    redirect_to posts_path
  else
    render :edit
  end
end
```

#### Refactoring: Add Before Actions
Execute a method before the controller calls the action.
Good for setting up instance variables, or to redirect based on some condition.

At the top of the controller:
```ruby before_action :set_post, only: [:show, :edit, :update]```

```ruby
def set_post
  @post = Post.find(params[:id])
end
``` 

#### Comments
Nested in a post.

Make a `comments_controller.rb` file
```ruby
def create
  @post = Post.find(params[:post_id])
  @comment = Comment.new(params.require(:comment).permit(:body))

  if @comment.save
    flash[:notice] = "Comment posted"
    redirect_to post_path(@post)
  else
    render 'posts/show'
end
```

Redirects point to a URL
Renders point to a template file

## Other Stuff
#### Binding.Pry
Add pry to POST controller action. Then run: `params`
Drill down on nested hash key: `post_params`
Require specific param, exclude others: `params.require(:post).permit(:title)`


##### Render vs Redirect

You should also understand what a render is vs a redirect. Render compiles the template into HTML and sends the HTML back as part of the response. Redirect sends back a URL as part of the response; there's no HTML in a redirect. Most browsers follow the redirected URL automatically, and a new request is issued

`<%= f.submit(@post.new_record? ? "Create Post" : "Update Post", class: 'btn btn-primary' %>`

#### Breadcrumbs
``` ruby
<% header = link_to('All Posts', posts_path) + " &raquo; #{@category.name}".html_safe %>

<%= render 'shared/content_title', title: header %>
```

obj

How to tell a model-backed form to submit to a nested structure? 
`<% form_for [@post, @comment] do |f| %>`
Create need both instance vars set up (eg `@post` and `@comment`).


To link off the website:
`<%= link_to @post.title, @post.url.starts_with?('http://') ? @post.url : "http://#{@post.url}" %>`
BUT better if it's a helper instead..

#### Helpers
Consolidate logic for views, but put in `application_helper.rb`.
Correlate with View templates. Good for repetivitve presentation logic.
Make methods available in a location. 
Open `application_helper.rb`
```ruby 
module ApplicationHelper
  def fix_url(str)
    str.starts_with?('http://') ? str : "http://#{str}"
  end
end

`fix_url(@post.url)`

#### Partials
Partials are html view fragments that repeat in your code. Convention: `_farts.html.erb`.

#### Checkboxes 
In form
##### Pure HTML Version
<select name='post[category_ids][]' multiple='multiple'> # setting key for submission. # name must have empty brackets if you expect an array-like structure # embedded in post nested hash
  <option value='1'>Option1</option>
  <option value='2'>Option2</option>
</select>

Using Helpers
<div class='control-group'>
    <%= f.collection_check_boxes :category_ids, Category.all, :id, :name do |box| %>
      <% box.label(class: "checkbox inline") {box.check_box(class: "checkbox") + box.text} %>
    <% end %> 
  </div>


Strong parameter syntax for arrays:
`params.require(:post).permit(:title, :url, category_ids [])`

## Review
Pattern for `create` and `update` actions:
```ruby
def create
    @post = Post.new(params.require(:post).permit(:url, :title, :description))

    if @post.save
        flash[:notice] = "Your post was saved."
        redirect_to posts_path
    else
        render :new
    end
end
```
```ruby
def update
    @post = Post.find(params[:id])

    if @post.update(params.require(:post).permit(:url, :title, :description))
        flash[:notice] = "Your post was updated."
        redirect_to post_path(@post)
    else
        render :edit
    end
end

Pattern for showing errors:
```ruby
<% if obj.errors.any? %>
    <div class="row">
        <div class="alert alert-error span8">
        <h5>Please fix the following errors:</h5>
        <ul>
        <% obj.errors.full_messages.each do |msg| %>
            <li><%= msg %></li>
        <% end %>
        </ul>
        </div>
    </div>
<% end %>
```

```

END