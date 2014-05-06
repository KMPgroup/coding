### Writing gems

If you have to implement the new feature to your app and you are thinking that in the future you may use it again in other project (lets say social networking site for turtles) - its good to start with packing it to an external gem at the beggining. 

Alternatively you can always place your code in /lib and extract it to a gem after developing, but
- it requires additional work,
- extraction could break something which you forgot to test.

#### So lets get started!

1. First of all in your current project create '/vendor/gems' dir and add it to .gitignore
  ```
      # .gitignore
      ...
      /vendor/gems
      ...
  ```
2. Then in that folder invoke `$bundle gem NAME-OF-YOUR-GEM`
3. Create new repo for your new gem and push it to the github
4. In your Gemfile add:
  ```
      ...
      if ENV['MY_BUNDLE_ENV'] == "dev"
        gem 'NAME-OF-YOUR-GEM', :path => "./vendor/gems/NAME-OF-YOUR-GEM"
      else
        gem 'NAME-OF-YOUR-GEM', :github => "YOURNAME/NAME-OF-YOUR-GEM"
      end
      ...
  ```

And this is it. Now in the terminal you can write `export MY_BUNDLE_ENV='dev'` and voila: you are using your local version. In production - github version will be used.

#### Problems

I encountered some problems during switching between gem versions: it is good to run `bundle update NAME-OF-YOUR-GEM` after each change of MY_BUNDLE_ENV

### What to do next

- listen or read [Ryan cast](http://railscasts.com/episodes/245-new-gem-with-bundler?view=asciicast)
