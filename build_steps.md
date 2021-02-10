## example code here: https://github.com/smithWEBtek/myapi3


## Build steps for simple Rails API, serving up an imported CSV file, using a custom method to obfuscate part of the data.

## 1. receive the csv file from client

determine models, columns, migrations

create new rails app in api mode
```
  $ rails new patient-data --api --database=postgresql
```

## 2. generate migration
```
  $ rails g resource patient first_name:text, last_name:text, email:text, 
diagnosis:text
```

look at the migration file that was created

`/app/db/migrations`

confirm the data types and column names

```
class CreatePatients < ActiveRecord::Migration[6.0]
  def change
    create_table :patients do |t|
      t.text :first_name
      t.text :last_name
      t.text :email
      t.text :diagnosis
      t.timestamps
    end
  end
end
```


## 3. run migrations

```
  $ rails db:migrate
```

## 4. add a validation to Patient model
```
class Patient
  validates :email, uniqueness: true
end
```

## 5. put csv file in: /app/lib/import/`cancer_study.csv`

set up csv import of data into Rails

start with test import method on Patient model

later we'll move it to a `rake task`

```
class Patient
  require 'csv'

  def self.import
    require 'csv'
    CSV.foreach(file, headers: true) do |row|
      create Patient object
      if save
        puts "#{p.email} record imported"
      else
        puts '#{p }-  record NOT imported'
      end
    end
  end

end
```

add pry gem to Gemfile in develop, test block

```
  gem 'pry'
```

run bundle install to install gems

```
$ bundle install
```




modify seeds.rb file, to use with rake task

(add dcms rake task)[https://gist.github.com/smithWEBtek/07ae565855801c0fe9ff066baf0b7ef0]

```
namespace :db do
  desc 'drop, create, migrate, seed the db, restart rails'
  task dcms: :environment do
    puts 'dropping db....'
    Rake::Task['db:drop'].invoke
    
    puts 'creating db....'
    Rake::Task['db:create'].invoke
    
    puts 'running migrations ....'
    Rake::Task['db:migrate'].invoke
    
    puts 'seeding db ....'
    Rake::Task['db:seed'].invoke
    
    puts 'starting rails server ....'
    exec('rails s')    
  end
end
```

notice that only id shows in JSON data

fix up seralizer

notice that diagnosis is plain readable string

add a `scramble` custom method to serializer




notice that you can't see only 1 ( show page ) for a patient

add show route to controller

namespace :db do
  desc 'drop, create, migrate, seed the db, restart rails'
  task dcms: :environment do
    puts 'dropping db....'
    Rake::Task['db:drop'].invoke
    
    puts 'creating db....'
    Rake::Task['db:create'].invoke
    
    puts 'running migrations ....'
    Rake::Task['db:migrate'].invoke
    
    puts 'seeding db ....'
    Rake::Task['db:seed'].invoke
    
    puts 'starting rails server ....'
    exec('rails s')    
  end
end




set up controller to serve json

add [active_model_serializers gem](https://github.com/rails-api/active_model_serializers/tree/0-10-stable)
for custom methods

` in Gemfile`
```
gem 'active_model_serializers', '~> 0.10.0'
```

bundle
rails g serializer patient




custom methods on Patient model

  def import
    # imports the csv data
    # creates an instance of Person, for each row of csv
  end

  def remove_phi
    # processes the Person table, remove the diagnosis data
  end

set up rake task to flush the database

set up controller to serve json

run app locally

