# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) {|repo_name| "https://github.com/#{repo_name}" }

# gem "rails"

gem "activerecord"

gem "rake", "~> 13.0"

gem "pry", "~> 0.14.0"

gem "sinatra-activerecord"

gem "require_all", "~> 3.0"

gem "shotgun", "~> 0.9.2"

gem "sinatra", "~> 2.1"

gem "bcrypt", "~> 3.1"

gem "dotenv", "~> 2.7"

gem "rack-flash3", "~> 1.0"

group :production do
  gem 'pg'
end

group :development, :test do
  gem 'sqlite3'
end