# Course 2 Week 3 Notes

These are my notes from the third week of Tealeaf second course on Rails Rapid Deployment.


## Authentication
#### Model-side

1. Add `bcrypt-ruby` to gemfile. Bcrypt does the password hashing algrorithm. `bundle install --without production`. Try '=3.0.1'. Restart server. 
2. Create password digest column in User. `rails generate migration add_pass_word_digest_to_users`. Add column: `add_column :users, :password_digest, :string`. rake db:migrate.
3. Add `has_secure_password validations: false` to `user.rb` model. 
Creates password virtual attribute, so you can set (but not get) u.password = "boobs" and it will make a password digest entry. 
`u.authenticate('inputstring')` if match, then retreive data, else, false. 

#### View-side

1. Add Users route: `resources :users, only: [:create]`
2. Add `users_controller.rb` add `new` and `create` actions. 
3. Add  `/views/users/new.html.erb` and `edit.html.erb` and `_form.html.erb` partial.
4. Add validations. `validates :password, presence: true, on: :create, length: {minimum: 3}`. 
5. Create named route. In `routes.rb`: `get '/register', to: 'users#new'`
6. Add nav button. 

#### Logging I/O

1. Create session routes. 
* `get '/register', to: 'users#new'`
* `get '/login', to: 'sessions#new'`
* `post '/login', to: 'sessions#create'`
* `get '/logout', to: 'sessions#destroy'`
2. Add sessions_controller.rb  
3. Add `/views/sessions/new.html.erb`. Add a non-model-backed form for logging in. 
4. Add logic to create and destroy.
5. Create `logged_in?` and `current_user` helpers for application_controller.rb
6. Expose helpers to views. `helper_method :current_user, :logged_in?
7. Add nav logic
8. Add `require_user` method into application_controller.rb
8. Add before action to controllers: `before_action :require_user, except:[:index, :show]


```ruby
class ApplicationController
  helper_method :current_user, :logged_in?

  def current_user
    @current_user ||= User.find(session[:user_id]) if session[user_id]
  end

  def logged_in?
    !!current_user
  end
end
```

Memoization

#### Asset Pipeline
Sprockets or Jammit is used for obfuscating, compressing and timestamping production assets.  

If running locally and you want to precompile all CSS and JS type files: `rake assets:precompile`.
Heroku does it automatically at deployment, so don't have an `/public/assets` folder. 