# Gemfile

Add
```
group :production do
  gem 'pg'
end
```

This will install postgres for production side

Then we want to create:
```
group :development, :test do
  gem 'pry'
  gem 'sqlite3'
end
```

Move gem 'pry' and 'sqlite3' into this group so that only test and development knows about these gems.

If you have specified versions of activerecord and sinatra-activerecord such as
```
gem 'activerecord', '~> 4.2', '>= 4.2.6', :require => 'active_record'
gem 'sinatra-activerecord', :require => 'sinatra/activerecord'
```

Then remove the versioning from them to:
```
gem "activerecord"
gem "sinatra-activerecord"
```

Delete your Gemfile.lock and `bundle install`

If bundle install fails due to the pg gem, it may not be able to find your pg_config file. Either 
- Add the PostgreSQL bin file to your path, via the instructions in their [documentation](https://postgresapp.com/documentation/install.html), (they make it really easy, it's one copy-and-paste bash command) OR
- Update path manually via your .profile file, OR
- Install the gem manually before you bundle install, specifying the pg_config path: 
```
gem install pg -- --with-pg-config=<PATH_TO_POSTGRE_PG_CONFIG_FILE>
```
For example:
```
gem install pg -- --with-pg-config=/Library/PostgreSQL/9.4/bin/pg_config
```


# DB

Go to your db folder and delete `development.sqlite`, `test.sqlite`, and `schema.rb` if those files exists. Make sure you only have a `migrate` folder (you can keep your migration files). If you have a `seeds.rb` file, that is totally fine, you can keep that as well.

Also make sure you specified the version in your migrate files. An example would look like:
```
class CreateUsers < ActiveRecord::Migration[4.2] << the [4.2] added to this specifies the version as 4.2
  def change
    create_table :users do |t|
      t.string :username
      t.string :password
    end
  end
end
```

# Config
Go to your config folder and create `database.yml`. We will setup our postgres database here.
```
development:
  adapter: postgresql
  encoding: unicode
  database: development
  pool: 2
production:
  adapter: postgresql
  encoding: unicode
  pool: 5
  host: <%= ENV['DATABASE_HOST'] %>
  database: production
```

in the environment.rb file remove:
```
ActiveRecord::Base.establish_connection(
  :adapter => "sqlite3",
  :database => "db/#{ENV['SINATRA_ENV']}.sqlite"
)
```

# Config.ru
Make sure to remove this line of code as well:
```
if ActiveRecord::Migrator.needs_migration?
  raise 'Migrations are pending. Run `rake db:migrate` to resolve the issue.'
end
```

# Running Postgres
You should have the postgres app installed. If you are on windows, you may need to run it, it'll be an elephant icon. If you are on Mac, you will see the icon at the top of your screen as an elephant icon. Click that icon and make sure the bottom of the drop down is marked green and says `stop`. If it says `start`, go ahead and start it.

Once it is started, run `rake db:create` and then `rake db:migrate`. If this command works, then postgres was setup correctly.

With postgres we have to create the databases before we migrate them, so the first time you migrate you will run `rake db:create` to create the databases. You will only have to run this once unless you drop your database.

# Heroku

Log into heroku, make sure the route ends with /apps. Click on `new` and `create new app`.

This should automatically put you in Deploy. Go to Deployment Method and select the github icon.

Go to connect to Github and do a search for your repository.

Once it finds your repository, click `connect`.

At the bottom where it says Manual Deploy. Click on `Deploy Branch`

Once it says `Your app was successfully deployed.`. Congratulations, your half way there!

# Heroku CLI
Navigate to https://devcenter.heroku.com/categories/command-line and install the heroku cli so we can use heroku in the terminal.

Type in the terminal `heroku login` and enter your heroku login details to log in the terminal.

# Migrating your database in Heroku
Navigate to your project and type `heroku apps` and find your app name.

Create a new project in heroku by adding `heroku apps:create <your-app-name-here>`

Now your git has a new remote. `git remote -v` you will see that heroku has added a git remote session. 

Make sure to add and commit changes

then type `heroku pg:reset DATABASE --confirm sinatra-movies-ft-020121` it will ask you to confirm with the name of your app, type in the name of your app and hit enter. This will create your database.

navigate back to heroku and click on the `more` button and select the console option.

Type in `rake db:migrate`

Congratulations you have configured heroku and deployed your sinatra app!

# helpful tips

### High Sierra
If you are having internal server issues and your OS is High Sierra, type in the terminal:
```
export OBJC_DISABLE_INITIALIZE_FORK_SAFETY=YES
```
It's a workaround an initializer issue that shotgun uses.

### useful heroku commands in the terminal
- `heroku apps` - lists all of your heroku apps by it's name
- `heroku git:remote <app name>` - connects your app to heroku
- `git push heroku master` - pushes your changes to heroku and re-deploys
- `heroku run <bash command>` - will run heroku console to execute a bash command
- `heroku pg:reset DATABASE` - will create your database (run this before you do your first migration)



### Useful resources
- Downloading Heroku CLI - https://devcenter.heroku.com/articles/heroku-cli#download-and-install
- Downloading Postgress app - https://postgresapp.com/downloads.html
- Heroku - https://dashboard.heroku.com/apps
- WSL Postgress set up - https://learning.flatironschool.com/courses/2609/pages/installing-databases-on-wsl2?module_item_id=150114
- Mac Postgres set up - https://learning.flatironschool.com/courses/2609/pages/installing-databases-on-macos?module_item_id=150105