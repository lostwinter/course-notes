# Rails [Tealeaf]
## Forms

These are my notes from week 2 of the Rapid Deployment course.

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

Need to set up object in controller: @post = Post.new

Common usage: `Post.create(params[:post])`

