### Creating new apps using templates

When creating a new app, always make a gemset in order to isolate your apps gems:

    $ rvm use ruby-2.0.0@myapp --ruby-version --create
    
Next install rails:

    $ gem install rails --no-ri --no-rdoc
    
Next generate the new application with the `rails new` command. The `rails new` command has a built in switch to use a template in order to generate a new app. The `-T` flag tells the generator to skip `test-unit`, as we are using rspec instead. In order to generate a new app use the following command:

    $ rails new myapp -m https://raw.github.com/RailsApps/rails-composer/master/composer.rb -T

The script will ask you a series of questions about your desired configuration. The first question is if you want to build a predefined app or a custom app. In most cases your answer should be custom app.

We use the following config:

 - database - MySQL (soon to change to PostgreSQL), if a NoSQL solution isn't needed (consult specification or supervisor)
 - template engine - Haml
 - unit testing - RSpec
 - integration testing - RSpec with Capybara
 - fixture replacement - FactoryGirl
 - frontend framework - Bootstrap or Zurb
 - mail sendind - SMTP
 - authentication - Devise
 - devise modules - usually default modules (consult specification or supervisor)
 - authorization - usually none (consult specification or supervisor)
 - form builder - SimpleForm
 - Create a project-specific rvm gemset and .rvmrc? - answer no

 
In case of any doubts, refer to [rails-composer readme][1]


  [1]: https://github.com/RailsApps/rails-composer/