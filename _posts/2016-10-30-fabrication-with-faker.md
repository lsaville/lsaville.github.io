---
title: Fabrication with Faker
layout: post
date: 2016-10-30
---

### For those times when you want your tests looking good...
  You might reach out to use factory girl because it's familiar. That makes sense. I felt the same way. Recently I worked to overcome that to learn a bit more about fabrication and what its all about. I'll share with you what I found.
<br>
<br>
  Check out fabrication at it's slick website [fabrication.org][0] or on [github][1]. The setup is very familiar if you've dealt with factory girl, just toss the line in your gemfile and make a a sub-folder `fabricators` in your rails project's spec folder. From there you can create a file called `fabricator.rb` where the actual magic happens.
<br>
<br>
  Additionally in my last project, I paired fabrication with [faker][2], a gem that spews out random data on command with fun categories like "hipster", "starwars", and "superhero". Again it's a simple add to your project's gemfile.
<br>
### In Action
  In the fabricator file itself one of the fabrications I set up was that of a user,
  ```ruby
  Fabricator(:user) do
    name { Faker::Name.name }
    email { Faker::Internet.email }
    password { "pass" }
    password_confirmation { "pass" }
    role 0
  end
  ```
  with utilization of faker in the name and email attributes. Additionally note that this user has a default role of 0. This allowed me to call on the fabricator in my tests for a normal user:<br>
  ```ruby
  user = Fabricate(:user)
  ```
  or an admin user by overriding the default:<br>
  ```ruby
  admin = Fabricate(:user, role: 1)
  ```
  In ActiveRecord there is the distinction between calling `new` and `create`, where `create` makes the object and persists it to the database while `new` simply makes the object without persisting it. In fabrication the default mode to to create as in:
  ```ruby
  Fabricate(:user)
  ```   
  You still have to option of building without persistence by using:
  ```ruby
  Fabricate.build(:user)
  ```
### Even More Action
  To make an object with a `belongs_to` association, you simply call the name of the object that `has_many` within the `belongs_to` object, like this:
  ```ruby
  Fabricator(:idea) do
    name { Faker::Superhero.name }
    user
    category
  end
  ```
  assuming that user and category are their own fabrications.<br><br>
  For a more complicated situation with images associated to an idea through a joins table:
  ```ruby
  Fabricator(:idea) do
    name { Faker::Superhero.name }
    user
    category
    after_build do |idea|
      3.times { Fabricate(:ideas_image, :idea => idea, :image => Fabricate(:image)) }
    end
  end

  Fabricator(:ideas_image) do
  end

  Fabricator(:image) do
    url { Faker::Avatar.image }
  end
  ```
  Where the idea now has the relations of three images, a user, and a category.
  <br>
### Seeing is Believing
  I'm always personally keen on seeing the 'thing'. The proof is in the pudding right? If you don't care skip this show section...(you might need to pump up that cmd+ to see better)<br><br>
  In a test set-up I have:
  ```ruby  
...
user = Fabricate(:user)
category = Fabricate(:category)
idea = Fabricate(:idea, user: user)
byebug
...
  ```
  ![fabrications show and tell in byebug][3]

### Conclusion
I hope what little I've touched on regarding fabrication is helpful if you needed a little push to get started. Big picture, I'm not clear on whether factory girl or fabrication is superior. In my experience fabrication syntax seemed slightly more intuitive, especially regarding the creation of relations between objects.<br><br>
Happy testing!

[0]: https://www.fabricationgem.org/
[1]: https://github.com/paulelliott/fabrication
[2]: https://github.com/stympy/faker
[3]: /images/fabricate-byebug.png
