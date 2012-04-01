# lrug-2012-04

!SLIDE

# Rails on JRuby
## Peter Vandenabeele

!SLIDE

# $ whoami</br>@peter_v

}}} images/IMG_2159.JPG

!SLIDE

# Why JRuby?

* Java
* speed
* stability

!SLIDE

# Starting point

A naive Rails app (rspec, haml, pg, ...)

```bash
$ rspec
.................................

Finished in 0.50589 seconds
33 examples, 0 failures
```
!SLIDE

# Turn on JRuby
 
```bash
$ rvm install jruby
Already installed jruby-1.6.7. ...

$ git diff v0.0.1..v0.0.2 .rvmrc | tail -2
-rvm use --create 1.9.3@lrug-jruby
+rvm use --create jruby@lrug-jruby

$ cd .. ; cd lrug-2012-04-demo/
Using /home/peterv/.rvm/gems/jruby-1.6.7 with gemset lrug-jruby

$ rspec
No command 'rspec' found ...

$ bundle install
...
```

!SLIDE

# Boooom!

}}} images/oracle_crash.jpg

!SLIDE

# FactoryGirl

```bash
$ bundle install
...
Gem::InstallError: factory_girl requires Ruby version >= 1.9.2.
An error occured while installing factory_girl (3.0.0), and Bundler cannot continue.
Make sure that `gem install factory_girl -v '3.0.0'` succeeds before bundling.
```

!SLIDE

# 1.9 compat mode

```bash
$ export JRUBY_OPTS=--1.9

$ ruby -v
jruby 1.6.7 (ruby-1.9.2-p312) (2012-02-22 3e82bc8) (Java HotSpot(TM) Server VM 1.6.0_26) ...

$ bundle install
...
Installing pg (0.13.2) with native extensions
...
Your bundle is complete! ...
```
