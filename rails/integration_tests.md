Integration tests with [capybara][1] and [site_prism][2]
--------------------------------------------------------

Guidelines:

 - Use site_prism for [PageObject Pattern][3]
 - Place your specs in `spec/features` folder
 - Place your Page Objects in `spec/support/pages`
 - Always use the [rspec expect syntax][4]
 - Install phantomjs (`brew install phantomjs`) and use [Poltergeist][5] as [Capybara][6] driver:


```ruby
Capybara.register_driver :poltergeist do |app|
  Capybara::Poltergeist::Driver.new(app, phantomjs_logger: WarningSuppressor)
end
Capybara.javascript_driver = :poltergeist
Capybara.app_host          = "http://0.0.0.0:9292"
Capybara.server_port       = 9292
```

In Mac OS 10.9 Mavericks, phantomjs has some sort of bug and logs out errors into spec output. To silence those errors (you know, until a fix arrives), add to your spec/support folder these 2 files:

 - **poltergeist.rb**
 
```ruby
module Capybara::Poltergeist
  class Client
    private
    def redirect_stdout
      prev = STDOUT.dup
      prev.autoclose = false
      $stdout = @write_io
      STDOUT.reopen(@write_io)

      prev = STDERR.dup
      prev.autoclose = false
      $stderr = @write_io
      STDERR.reopen(@write_io)
      yield
    ensure
      STDOUT.reopen(prev)
      $stdout = STDOUT
      STDERR.reopen(prev)
      $stderr = STDERR
    end
  end
end
```

 - **warning_supressor.rb**

```ruby
class WarningSuppressor
  class << self
    def write(message)
      if message =~ /QFont::setPixelSize: Pixel size <= 0/ || message =~/CoreText performance note:/ then 0 else puts(message);1;end
    end
  end
end
```

When using [devise][7] (which is the case 99% of the time), create a Sign In page object:
```ruby
class UserSignIn < SitePrism::Basic
  set_url '/users/sign_in'
  element :email, '#user_email'
  element :password, '#user_password'
  element :sign_in_button, '.btn-primary' # CSS selector of form submit button

  # Performs basic the basic sign in process
  #
  def proceed
    self.email.set 'foo@bar.pl' # fill in email input
    self.password.set 'foobar'  # fill in password input
    self.sign_in_button.click   # click the button
  end
end
```

and use it in the top level of your `describe`:
```ruby
describe "SomeTest" do

  before(:each) do
    FactoryGirl.create(:user)
    # #load is a method of site_prism, it basically runs visit some_path, 
    # where some_path is defined inside the page object
    UserSignIn.new.load.proceed
  end
  
  # Omitted for brewity

end
```

There is one caveat, when you are writing javascript specs (`js: true`), that you have to keep in mind. Poltergeist (the phantomjs capybara driver) uses a seperate database connection ([stackoverflow][8]). 

Because of this, the session is lost, so when you visit the sign in page in a `before` block and submit the form, the user session will be lost when you are redirected and thus you will be back on the sign in page.

To fix that create file `support/share_db_connection.rb`, with the following contents:

```ruby
class ActiveRecord::Base
  mattr_accessor :shared_connection
  @@shared_connection = nil
 
  def self.connection
    @@shared_connection || retrieve_connection
  end
end
 
# Forces all threads to share the same connection. This works on
# Capybara because it starts the web server in a thread.
ActiveRecord::Base.shared_connection = ActiveRecord::Base.connection
```

Now all will work as expected.


With this setup you are ready to write killer integration specs.


  [1]: https://github.com/jnicklas/capybara
  [2]: https://github.com/natritmeyer/site_prism
  [3]: http://blog.josephwilk.net/cucumber/page-object-pattern.html
  [4]: http://betterspecs.org/#expect
  [5]: https://github.com/jonleighton/poltergeist
  [6]: https://github.com/jnicklas/capybara
  [7]: https://github.com/plataformatec/devise
  [8]: http://stackoverflow.com/questions/18623661/why-is-capybara-discarding-my-session-after-one-event