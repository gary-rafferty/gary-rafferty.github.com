---
layout: post
title: Integration testing Devise with RSpec and Capybara
author: Gary Rafferty
meta_keywords: Devise,RSpec,Capybara,Testing,Rails
meta_description: How I test devise using rspec and capybara
---
# {{ page.title }}
 
I use Rails quite a bit for development and recently noticed that my unit tests were quite indepth but that my integration tests were lacking.   
So I recently had a flick through [Rails Test Prescriptions](http://pragprog.com/book/nrtest/rails-test-prescriptions), then took a gander at other people's code on Github, and decided  
to make more of an effort with testing.

I usually use the [Devise](https://github.com/plataformatec/devise) gem for authentication and figured that a good place to start  
would be to test the registration and login process.

The following example assumes [RSpec](http://rspec.info) and [Capybara](https://github.com/jnicklas/capybara) are installed.  
If not, just stick the following in yout Gemfile and let Bundler sort things.

```ruby
  group :test do
    gem 'turn', :require => false
    gem 'factory_girl_rails'
    gem 'capybara',
    gem 'rspec-rails'
    gem 'guard-rspec',
    gem 'database_cleaner'
  end
```

You don't need all these but I've just copied it from my Gemfile.

So the first thing to do here is test that a user can register, and if successful, will be brought to the Welcome page.  

```ruby
  describe 'UserRegistration' do
    it 'allows a user to register' do
      visit new_user_registration_path
      fill_in 'First name', :with => 'New'
      fill_in 'Last name', :with => 'User'
      fill_in 'Email', :with => 'newuser@example.com'
      fill_in 'Password', :with => 'userpassword'
      fill_in 'Password Confirmation', :with => 'userpassword'
      click_button 'Register'
      page.should have content 'Welcome'
    end
  end
```

The above code describes how a user can visit the signup page, enter in some valid details, click Register, and the new  
page should contain the text Welcome.

The next step is to test the login functionality and the following test takes care of that.  
I've used [FactoryGirl](https://github.com/thoughtbot/factory_girl) instead of regular fixtures.

```ruby
  describe "UserSignin" do
    let(:user) { Factory :user }

    it "should allow a registered user to sign in" do
      visit new_user_session_path
      fill_in "Email", :with => user.email
      fill_in "Password", :with => user.password
      click_button "Sign in"
      page.should have_content("Welcome")
    end

    it "should not allow an unregistered user to sign in" do
      visit new_user_session_path
      fill_in "Email", :with => "notarealuser@example.com"
      fill_in "Password", :with => "fakepassword"
      click_button "Sign in"
      page.should_not have_content("Welcome")
    end
  end
```

The above snippet tests that a user with valid credentials be allowed to log in, and a user with invalid credential not be.  
Simple and readable. Though not an overly complex example, you probably get the gist of their usage.
