---
title:  "Get rid of the DL deprecated message from Pry and IRB in Ruby 2.0 on Windows"
date:   2014-04-25 15:08:00 GMT
tags:   ruby pry dl deprecated
---
It's a commonly known fact that in Ruby 2.0 on Windows, Pry and IRB display this infamous message 

> DL is deprecated, please use Fiddle

This can get annoying after some time. If you really want to remove this message, I found a method to do it and it's actually quite simple.

Start off by installing the `rb-readline` gem, by Luis Lavena, of [RubyInstaller](http://rubyinstaller.org) fame:

```
gem install rb-readline
```

This is a pure ruby implementation of Readline that doesn't require DL.

Let's start with Pry. First, open or create your `.pryrc` file. I'm not going to explain what the file is. You can find that information [here](https://github.com/pry/pry/wiki/Pry-rc). The file should be in `%HOMEDRIVE%%HOMEPATH%`. If it isn't, then you need to create one through the command prompt, since Windows Explorer doesn't like creating files that start with a dot. This command will create an empty `.pryrc` file from the command prompt for you:

```
echo. 2> %HOMEDRIVE%%HOMEPATH%\.pryrc
```

Editing the file, include the following:

```ruby
gem 'rb-readline'
```

Now, if you open Pry the message is gone. Victory!

Unfortunately, `.irbrc` doesn't work the same way that `.pryrc` does. IRB actually loads `.irbrc` only after requiring `readline`, but if you don't mind editing code that came with Ruby, then follow on.

Go to the `bin` directory of your Ruby installation, and open the `irb` file (not the `irb.bat` file), and add the same gem line from above, before `require "irb"`:

```ruby
gem 'rb-readline'
```

If you open IRB, the message is now gone.

![victory baby](/assets/images/victory_baby.png)
