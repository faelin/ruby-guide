---
title: Ruby on Rails Tutorial, pt. 2 – Rails
description: Overview of the "Rails" part of Ruby on Rails
tags: ruby, ruby on rails, coding, language, tutorial, learning
---

# Rails Guide

## Table of Contents
> [TOC]

---
# Getting started with Rails

---
## Rails Guides (https://guides.rubyonrails.org)
For more information about Rails, we highly recommend that you peruse the official [Getting Started](https://guides.rubyonrails.org/getting_started.html) guides. The most useful sections are linked here:
  * ActiveRecord — Rails Object-Relational Mapping
  	- [Active Record Basics](https://guides.rubyonrails.org/active_record_basics.html)
  	- [Active Record Migrations](https://guides.rubyonrails.org/active_record_migrations.html) — constructing your db schema through Rails
  	- [Active Record Associations](https://guides.rubyonrails.org/association_basics.html) — the "relationial" part of the ORM
  	- [Active Record Query Interface](https://guides.rubyonrails.org/active_record_querying.html) — Rails DSL for querying the backing database
  * Views — HTML Rendering
  	- [Action View Overview](https://guides.rubyonrails.org/action_view_overview.html)
  	- [Layout and Rendering](https://guides.rubyonrails.org/layouts_and_rendering.html)
  * Controllers — RESTful Logic
  	- [Action Controller Overview](https://guides.rubyonrails.org/action_controller_overview.html)
  	- [Rails Routing](https://guides.rubyonrails.org/routing.html)
  
---
## Building a Demo App

We are going to use `Ruby 2.5` and `Rails 6`.

To start, make a directory named `rails-test`, then create the following file inside of it:
```dockerfile
# rails-test/Dockerfile

FROM ruby:2.5

RUN apt-get update && \
    apt-get install -y --no-install-recommends \
        nodejs npm

RUN npm update && npm install -g yarn  

RUN gem update --system && \
    gem install rake && \
    gem install bundler && \
    gem install rails 

WORKDIR /usr/src/app

RUN rails new . && bundle install

EXPOSE 3000
CMD bundle exec rails server -b 0.0.0.0
```

```bash
cd rails-test

docker build -t rails-test .

docker run -it --rm -v $(pwd)/.:/usr/src/app rails-test bash -c "rails new . && bundle install && bundle exec rails webpacker:install"

docker run -it --rm -p 3000:3000 -v $(pwd)/.:/usr/src/app rails-test

```

---
## Rails Apps are All-in-One Webserver Environments

---
### Directory Structure
Rails apps begin with the following datastructure:
```
.
│
├── app
│    ├── assets
│    │    ├── images
│    │    ├── javascripts
│    │    │    └── application.js
│    │    |
│    │    └── stylesheets
│    │        └── application.css
│    │
│    ├── controllers
│    │    ├── application_controller.rb
│    │    └── concerns
│    │
│    ├── helpers
│    │    └── application_helper.rb
│    │
│    ├── mailers
│    ├── models
│    │    └── concerns
│    │
│    └── views
│        └── layouts
│            └── application.html.erb
│
├── bin
│    ├── bundle
│    ├── rails
│    ├── rake
│    ├── setup
│    └── spring
│
├── config
│    ├── application.rb
│    ├── boot.rb
│    ├── database.yml
│    ├── environment.rb
│    ├── environments
│    │    ├── development.rb
│    │    ├── production.rb
│    │    └── test.rb
│    │
│    ├── initializers
│    │    ├── assets.rb
│    │    ├── backtrace_silencers.rb
│    │    ├── cookies_serializer.rb
│    │    ├── filter_parameter_logging.rb
│    │    ├── inflections.rb
│    │    ├── mime_types.rb
│    │    ├── session_store.rb
│    │    └── wrap_parameters.rb
│    │
│    ├── locales
│    │    └── en.yml
│    │
│    ├── routes.rb
│    └── secrets.yml
│
├── config.ru
├── db
│    └── seeds.rb
│
├── Gemfile
├── Gemfile.lock
├── lib
│    ├── assets
│    └── tasks
│
├── log
├── public
│    ├── 404.html
│    ├── 422.html
│    ├── 500.html
│    ├── favicon.ico
│    └── robots.txt
│
├── Rakefile
├── README.rdoc
├── test
│    ├── controllers
│    ├── fixtures
│    ├── helpers
│    ├── integration
│    ├── mailers
│    ├── models
│    └── test_helper.rb
│
├── tmp
│    └── cache
│         └── assets
│
└── vendor
     └── assets
          ├── javascripts
          └── stylesheets
```



| Location | Description |
|:---------|:------------|
| app | Core directory of the entire app; most of the application-specific code will go into this directory. |
| app/assets | Contains the static files required for the application’s front-end grouped into folders based on their type. |
| app/controllers | Controllers are responsible for orchestrating the model and views. Controllers handle the logic of parsing queries. |
| app/controllers/application_controller.rb | This is the main controller from which all other controllers inherit. The methods on `ApplicationController` are available to other controllers as well. This controller inherits from the `ActionController::Base` module, which has set of methods to work with in controllers. |
| app/helpers | This is where all the helper functions for views reside. |
| app/controllers/application_helper.rb | Similar to the `ApplicationController`, This is the main helper from which all other helpers inherit. |
| app/models | All model files live in the app/models directory. Models act as object-relational maps to the database tables that hold the application data. |
| app/views | Files related to Views (HTML templates used to render webpages) go into this directory. The files are a combination of HTML and Ruby (called Embedded Ruby or Erb) and are organized based on the controller to which they correspond. There is a view file for each controller action. |
| config | As the name suggests this contains all the application’s configuration files. The database connection and application behavior can be altered by the files inside this directory. |
| config/environments | This folder contains the environment-specific configuration files for the development, test, and production environments. |
| config/initializers | Any `*.rb` file you create here will run during the Rails initialization, automatically. |
| db | All the database related files go inside this folder. The configuration, schema, and migration files can be found here, along with any seed files. |
| Gemfile | The `Gemfile` is the place where all your app’s gem dependencies are declared. This file is mandatory, as it includes the Rails core gems, among other gems. |
| Gemfile.lock | `Gemfile.lock` holds the gem dependency tree, including all versions, for the app. This file is generated by `bundle install` on the above `Gemfile`. It, in effect, locks your app dependencies to specific versions. |
| lib | This directory is where all the application specific libraries goes. Application specific libraries are re-usable generic code extracted from the application. |
| log | This holds all the log files. Rails automatically creates files for each environment. |
| test | The folder name says it all. This holds all the test files for the app. A subdirectory is created for each component’s test files. |

---
## Rails ActiveRecord
In Active Record, objects carry both persistent data and behavior which operates on that data. Active Record takes the opinion that ensuring data access logic as part of the object will educate users of that object on how to write to and read from the database.

Another way of thinking about it is that Active Record lets you create a Ruby object that represents a row in one of your database tables (such as a `User`):
```ruby
User.new(name: "Harold", surname: "Riddler", age: 37)

    ...

u = User.find_by(surname: "Riddler")
u.age += 1
u.save
```
> Rails ActiveRecord is, as the name implies, an implementation of the ["active record" architectural pattern](https://en.wikipedia.org/wiki/Active_record_pattern).

---
### Models
Rails leverages Object Relational Mapping via "Models" to connect objects in your app to the tables described in your schema.

Suppose that you wanted to create an online e-reader app. Perhaps you want users to be able to navigate directly to a particular chapter, with each chapter containing a link to the next, and each "book" containing a list of chapters. Let's start by creating the basic "chunk" of our service, the Chapter:
```ruby
class Chapter < ApplicationRecord
  belongs_to :book
end
```
Our `Chapter` Model now contains only one line: the `belongs_to` statement is known as an "Association". This declaration simply says "each chapter belongs to one book" and corresponds to the `book_id` foreign key in our Chapters table.

However, Rails wants us to also add a reference on the other side of the association:
```ruby
class Book < ApplicationRecord
  has_many :chapters
  validates :title, presence: true
end
```
These two declarations enable a good bit of automatic behavior. For example, if you have an instance variable `@book` containing a Book, you can retrieve all the Chapters belonging to that Book as an array using `@book.chapters`. The `validates` declaration simply ensures that any new Book is provided a "title" argument.

There are four main forms of association:
|  Association |Usage Example | Relationship |
|:------------|:------|:----:|
| belongs_to | `@book.owner` | `one-to-one` |
| has_one | `@book.jacket` | `one-to-one` |
| has_many | `@book.chapters` | `one-to-many` |
| has_and_belongs_to_many | `@book.authors` | `many-to-many` |

```ruby
class Book < ApplicationRecord
    belongs_to :owner
    has_one :jacket
    has_many :chapters
    has_and_belongs_to_many :authors
end

class Jacket < ApplicationRecord
    belongs_to :book
end

class Chapter < ApplicationRecord
    belongs_to :book
end

class Author < ApplicationRecord
    has_many :books
end
```
> For more information about ActiveRecord Associations, take a look at the [ActiveRecord Guide](https://guides.rubyonrails.org/association_basics.html).

---
### The `generate` Command
Rails provides a convenient CLI templating mechanism via the the `generate` command:
```bash
$ rails generate scaffold Book author:string title:string
```
This command will generate several files, including:

|   File   | Purpose  |
|:---------|:---------|
| db/migrate/*[datetime]*_create_books.rb | Migration to create the Books table in your database |
| app/controllers/books_controller.rb | The Book controller |
| app/models/book.rb | The Book model |
| app/views/_books.html.erb | The Book view |
| test/controllers/books_controller_test.rb | Testing harness for the Books controller |
| test/models/book_test.rb | Testing harness for the Book model |
| test/fixtures/books.yml | Sample Books for use in testing |

To create Models that relate to each other, we simple add a `reference` attribute:
```bash
$ rails generate Model Chapter number:integer body:text book:references
```
This templating command will generate a similar set of files:

|   File   | Purpose  |
|:---------|:---------|
| db/migrate/*[datetime]*_create_chapters.rb | Migration to create the Chapter table in your database |
| app/models/chapter.rb | The Chapter model |
| test/models/chapter_test.rb | Testing harness for the Chapter model |
| test/fixtures/chapters.yml | Sample Chapters for use in testing |

Notice that the `scaffold` generation added an extra `controller` and a `view` file for the Book model. These files manage the RESTful API and the HTML response, respectively.

Let's look at what those files do:

---
### Migrations
Migrations are Ruby classes that are designed to make it simple to create and modify database tables. Rails uses rake commands to run migrations, and it's possible to undo a migration after it's been applied to your database. Migration filenames include a timestamp to ensure that they're processed in the order that they were created.

Let's look at the `create_books` migration
```ruby
class CreateBooks < ActiveRecord::Migration[6.0]
  def change
    create_table :books do |t|
      t.string :author
      t.string :title
 
      t.timestamps
    end
  end
end
```
With just this one migration file, we could create a Books table via
```bash
$ rails db:migrate
```
which yields something like
```
==  CreateBooks: migrating ==================================================
-- create_table(:books)
   -> 0.0019s
==  CreateBooks: migrated (0.0020s) =========================================
```

However, we have created more than one migration file. In its most basic form, `migrate` runs the `change` or `up` methods for all the migrations that have not yet been run.

Let's look at the second migration
```ruby
class CreateChapters < ActiveRecord::Migration[6.0]
  def change
    create_table :chapters do |t|
      t.integer :number
      t.text :body
      t.references :book, null: false, foreign_key: true
 
      t.timestamps
    end
  end
end
```
The `t.references` line creates an integer column called book_id, an index for it, and a foreign key constraint that points to the id column of the books table. Let's look at the rest of our migration:
```
==  CreateChapters: migrating =================================================
-- create_table(:chapters)
   -> 0.0115s
==  CreateChapters: migrated (0.0119s) ========================================
```

At this point, our database schema is outlined in the `db/schema.rb` file:
```ruby
ActiveRecord::Schema.define(version: 20190114194744) do

  create_table "book_chapters", force: :cascade, options: "ENGINE=InnoDB DEFAULT CHARSET=utf8" do |t|
    t.integer "number"
    t.text "body"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
    t.bigint "book_id"
    t.index ["book_id"], name: "index_book_chapters_on_book_id"
  end

  create_table "books", force: :cascade, options: "ENGINE=InnoDB DEFAULT CHARSET=utf8" do |t|
    t.string "author"
    t.string "title"
    t.datetime "created_at", null: false
    t.datetime "updated_at", null: false
  end

end
```
>> [color=#FF0000] N.B. the `schema.rb` is generated by the `migrate` command to reflect the current state of your database. Modifying this file will not result in any changes to your db.

---
## Rails Actions

---
### Routing and Controllers
Routes tell the application how to "wire" an HTTP request to a Controller. By default routes are handled in `/config/routes.rb`. Controllers turn these requests into specific actions or data. Controllers can respond in many ways including json, xml, and html.

In the `routes.rb` file, you can specify new routes, optionally with specific parameters:
```ruby
# Send GET HTTP requests from /patients url 
# to the `index` method of the `patients` 
# controller.
get '/patients', to: 'patients#index'

# Send GET HTTP requests from /patients/1 to
# the `show` method of the `patients`
# controller.
get '/patients/:id', to: 'patients#show'
```

The controller then gains access to the provided route- and query-parameters
```ruby 
class PatientsController < ApplicationController
  def index
    @books = Patient.all
  end
  
  def show
    @patient = Patient.find id: params[:id]
  end
end
```

---
### Params 
The params hash is a copy of variables from the POST body, the GET query, and the URL path (listed here in order of precedence, lowest to highest).

---
### RESTing our Resources
In Rails, a resourceful route provides a mapping between HTTP verbs and URLs to controller actions. By convention, each action also maps to a specific CRUD operation in a database. A single entry in the routing file, such as:
```ruby
resources :photos
```
creates seven different routes in your application, all mapping to the `Photos` controller:
| HTTP Verb | Path             | Controller#Action | Used for |
|:----------|:-----------------|:------------------|:---------|
| GET       | /photos          | photos#index      | display a list of all photos |
| GET       | /photos/new      | photos#new        | return an HTML form for creating a new photo |
| POST      | /photos          | photos#create     | create a new photo |
| GET       | /photos/:id      | photos#show       | display a specific photo |
| GET       | /photos/:id/edit | photos#edit       | return an HTTML form for editing a photo |
| PATCH/PUT | /photos/:id      | photos#update     | update a specific photo |
| DELETE    | /photos/:id      | photos#destroy    | delete a specific photo |

![Now that's efficient!](https://i.imgur.com/SM1PN1t.jpg)
</br>

Resources can be nested to simplify object-relationships:
```ruby
resources :authors do
  resources :books, only: [:index, :new, :create]
end
resources :books, only: [:show, :edit, :update, :destroy]
```
By only nesting the `#index`, `#new`, and `#create` actions under the `authors` route, we can simplify cumbersome paths:
```
[GET] /authors/1/books/5/edit

# instead of the nested route above, we simplify to

[GET] /books/5/edit
```

>> [color=#FF0000] N.B. it is generally a good rule of thumb that resources should never be nested more than 1 level deep. You can read more about why this is, by following [this finely crafted link](http://weblog.jamisbuck.org/2007/2/5/nesting-resources).

You are not limited to the default RESTful routes that Resources provide! If you like, you may add additional routes that apply to specific routes.

---
### Views
The default templating language for Rails is called ERB (Embedded Ruby).

ERB files are run in the context of a Rails controller. They are just simple text files with ruby embedded certain tags. This ruby is

```ruby
<% Ruby code -- inline with output %>
<%= Ruby expression -- replace with result %>
<%# comment -- ignored -- useful in testing %>
% a line of Ruby code -- treated as <% line %>
%% replaced with % if first thing on a line and % processing is used
<%% or %%> -- replace with <% or %> respectively
```

---
## Naming Conventions
> Taken from github user [`iangreenleaf`](https://gist.github.com/iangreenleaf/b206d09c587e8fc6399e)

### General Ruby conventions

Class names are `CamelCase`.

Methods and variables are `snake_case`.

Methods with a `?` suffix will return a boolean.

Methods with a `!` suffix mean one of two things: either the method operates destructively in some fashion, or it will raise and exception instead of failing (such as Rails models' `#save!` vs. `#save`).

In documentation, `::method_name` denotes a *class method*, while `#method_name` denotes a *instance method*.

### Database

*Database tables* use `snake_case`. Table names are **plural**.

*Column names* in the database use `snake_case`, but are generally **singular**.

Example:
```
+--------------------------+
| bigfoot_sightings        |
+------------+-------------+
| id         | ID          |
| sighted_at | DATETIME    |
| location   | STRING      |
| profile_id | FOREIGN KEY |
+------------+-------------+

+------------------------------+
| profiles                     |
+---------------------+--------+
| id                  | ID     |
| name                | STRING |
| years_of_experience | INT    |
+---------------------+--------+
```

### Model

Model *class names* use `CamelCase`. These are **singular**, and will map automatically to the plural database table name.

Model *attributes* and *methods* use `snake_case` and match the column names in the database.

Model files go in `app/models/#{singular_model_name}.rb`.

Example:

```ruby
# app/models/bigfoot_sighting.rb
class BigfootSighting < ActiveRecord::Base
  # This class will have these attributes: id, sighted_at, location
end
```
```ruby
# app/models/profile.rb
class Profile < ActiveRecord::Base
  # Methods follow the same conventions as attributes
  def veteran_hunter?
    years_of_experience > 2
  end
end
```

#### Relations in models

Relations use `snake_case` and follow the type of relation, so `has_one` and `belongs_to` are **singular** while `has_many` is **plural**.

Rails expects foreign keys in the database to have an `_id` suffix, and will map relations to those keys automatically if the names line up.

Example:

```ruby
# app/models/bigfoot_sighting.rb
class BigfootSighting < ActiveRecord::Base
  # This knows to use the profile_id field in the database
  belongs_to :profile
end
```
```ruby
# app/models/profile.rb
class Profile < ActiveRecord::Base
  # This knows to look at the BigfootSighting class and find the foreign key in that table
  has_many :bigfoot_sightings
end
```

### Controllers

Controller *class names* use `CamelCase` and have `Controller` as a suffix. The `Controller` suffix is always singular. The name of the resource is usually **plural**.

Controller *actions* use `snake_case` and usually match the standard route names Rails defines (`index`, `show`, `new`, `create`, `edit`, `update`, `delete`).

Controller files go in `app/controllers/#{resource_name}_controller.rb`.

Example:

```ruby
# app/controllers/bigfoot_sightings_controller.rb
BigfootSightingsController < ApplicationController
  def index
    # ...
  end
  def show
    # ...
  end
  # etc
end
```

```ruby
# app/controllers/profiles_controller.rb
ProfilesController < ApplicationController
  def show
    # ...
  end
  # etc
end
```

### Routes

Route names are `snake_case`, and usually match the controller. Most of the time routes are **plural** and use the plural `resources`.

[Singular routes](http://edgeguides.rubyonrails.org/routing.html#singular-resources) are a special case. These use the singular `resource` and a singular resource name. However, they still map to a plural controller by default!

Example:

```ruby
resources :bigfoot_sightings
# Users can only see their own profiles, so we'll use `/profile` instead
# of putting an id in the URL.
resource :profile
```

### Views

View file names, by default, match the controller and action that they are tied to.

Views go in `app/views/#{resource_name}/#{action_name}.html.erb`.

Examples:

 * `app/views/bigfoot_sightings/index.html.erb`
 * `app/views/bigfoot_sightings/show.html.erb`
 * `app/views/profile/show.html.erb`

# More resources #

* [ActiveRecord naming and schema conventions (including magic column names)](http://edgeguides.rubyonrails.org/active_record_basics.html#naming-conventions)
* [Mind map of Rails conventions](https://teddicodes.files.wordpress.com/2015/02/railsnamingconventions.pdf)






















