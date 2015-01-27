# Rails [Tealeaf]
## Forms

These are my notes from week 2 of the Rapid Deployment course.

#### Strong Parameters
In controller file eg: posts_controller.rb

```ruby
private
def post_params
  params.require(:post).permit(:title, :url, :creator)
  # permit! to allow all.
end
```

Don't use pure HTML. Opt for Model-Backed Forms
#### Rails Form Helper
```ruby
<%= form_tag '/posts' do %>
  <%= label_tag :title %>
  <%= text_field_tag :title, "Input a title" %>
  <br/>
  <%= submit_tag "Create Post", class: 'btn btn-primary' %>
<% end %>
```

#### Rails Model-Backed Forms
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