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
------------------
#### Creation Procedure
1. `rails new myapp`
2. Checkout databases.yml, then gemfile, then routes, then readme.
3. Check routes: `rake routes`. 
4. Start server. For Cloud9: `rails s -p $PORT -b $IP`
5. Set up Git.
6. Deploy to Heroku: `heroku create`, `git push heroku master`. To urn migrations on Heroku use `heroku run rake db:migrate`.

## Models
Setup and modify db tables with migrations: 
1. `rails generate migration create_posts`. Set up tables in migration.
2. `rake db:migrate`
3.  Create a model for the table: 
```ruby class Post < ActiveRecord::Base
end```

