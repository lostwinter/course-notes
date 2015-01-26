# Rails [Tealeaf]
## Database and Model Setup

These are my notes from week 1 of the Rapid Deployment course.

#### Directory Structure
**Assets** hold static files like images, CSS and JS.

**Controllers** are .rb files that like routes to actions. `application_controller.rb` is the parent.

**Helpers** extract common logic used in Views.

**Bin** holds executables like `rake` and `bundle`.

**Config > Initializers** store apps that your source code can use.

**DB** holds sqlite3 files. If using another DB, this will be empty.

#### Creation Procedure
1. `rails new myapp`
2. Checkout databases.yml, then gemfile, then routes, then readme.
3. Check routes: `rake routes`. 
4. Start server. For Cloud9: `rails s -p $PORT -b $IP`
5. Set up Git.
6. Deploy to Heroku: `heroku create`, `git push heroku master`. To urn migrations on Heroku use `heroku run rake db:migrate`.

## Models
1. Setup and modify db tables with migrations: `rails generate migration create_posts`. Set up tables in migration.
2. `rake db:migrate`
3.  Create a model for the table: 
```ruby class Post < ActiveRecord::Base```
4. Verify. `rails c`. Create a record.
5. Add columns with `rails g migration add_user_id_to_posts`. In the migration, ```ruby add_column :posts, :user_id, :integer``` (table, column, type).


`rake db:setup`
`rake db:drop`
`rake db:create`
`rake db:migrate`
`rake db:rollback STEP=3`

#### What is ActiveRecord?
ActiveRecord is an object relational mapper (ORM). It determines how the DB becomes code. Turns ruby commands into SQL.
* Each row correlates to an object
* Each column has getters and setters.

#### User.find(1)
`User.take(2)` Draws 2 records from user table
`User.first` or `User.first(3)`
`User.last `
`User.find_by name: ‘Bobby’`
`User.where("orders_count = ?", params[:orders])`
`User.select(“column1, column3”)`
`User.create(title: ‘Farts’, url: ‘www.farts.com’, user: User.first)`

#### Naming Conventions
* class Group < ActiveRecord::Base
* group.rb # singular_snake_case
* groups # table

#### 1:1 

#### 1:M

#### M:M
The User model:
```ruby 
class User < ActiveRecord::Base
    has_many :user_groups, foreign_key: :user_id
    has_many :groups, through: :user_groups
end
```

The User_Group model:
```ruby 
class UserGroup < ActiveRecord::Base
    belongs_to :user, foreign_key: :user_id
    belongs_to :group, foreign_key: :group_id
end
```


The Group model:
```ruby
class Group < ActiveRecord::Base
    has_many :user_groups, foreign_key: :group_id
    has_many :users, through: :user_groups
end
```

After setting up associations, set up join in console. Then link foreign key.

1. `Post.first`
2. `pc = PostCategory.create`
3. `post.post_categories`
4. `pc.post`
5. `pc.post = post`
6. `pc.save`
7. Check: `post.reload.post_categories`
8. category = Category.first
9. pc = PostCategory.first
10. category.post_categories << pc


## Routes
<%= link_to ‘Go back to posts’, ‘/posts’ %>   #better to use named url, then add _path

posts_path  

<%= render @posts %>   # is the same as:

<% @posts.each do |post| %>
<%= render ‘posts.post’, post: post %>
<% end %


### Questions.
* Is there are way/reason to consolidate migrations? like if you have 30 migrations


