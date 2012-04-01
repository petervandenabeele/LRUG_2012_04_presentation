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

!SLIDE

# postgresql adapter

```bash
$ rspec
LoadError: load error: pg_ext -- java.lang.UnsatisfiedLinkError:
failed to load shim library, error: /usr/lib/libstdc++.so.6:
version `GLIBCXX_3.4.14' not found (required by /home/peterv/
.rvm/rubies/jruby-1.6.7/lib/native/i386-Linux/libjruby-cext.so)
```
gem 'pg' has native extensions

!SLIDE

# jdbcpostgresql

```ruby
$ git diff -U0 v0.0.2..v0.0.3 Gemfile | tail -4
-gem 'pg'
+gem 'pg', :platform => :ruby
+gem 'activerecord-jdbcpostgresql-adapter', :platform => :jruby
+gem 'jruby-openssl', :platform => :jruby

$ git diff -U0 v0.0.2..v0.0.3 config/database.yml | tail -1
+  adapter: postgresql # no "jdbcpostgresql" needed here

$ rspec
```

Note: Connection over tcp/ip socket (not UNIX domain socket)

!SLIDE

# slow start-up?

```bash
$ time rspec
.................................

Finished in 2.97 seconds
33 examples, 0 failures

real	0m18.438s
user	0m29.222s
sys	0m1.552s

```

!SLIDE

# compare MRI

```bash
$ rvm use 1.9.3@lrug-jruby
Using /home/peterv/.rvm/gems/ruby-1.9.3-p125 with gemset lrug-jruby
peterv@e6500:~/b/github/petervandenabeele/lrug-2012-04-demo$ time rspec
.................................

Finished in 0.51011 seconds
33 examples, 0 failures

real	0m4.120s
user	0m3.740s
sys	0m0.304s
```
!SLIDE

# Nailgun

From the book (untested)

```bash
$ jruby --ng-server

$ jruby --ng program.rb
```
!SLIDE

# HBase

```ruby
HADOOP_HOME = ENV['HADOOP_HOME']
...
include Java
require 'java'
$CLASSPATH << "#{HADOOP_HOME}/"
...
puts require 'hadoop-core.jar'
...
import org.apache.hadoop.conf.Configuration
...
def self.create_hbase_table(table_name)
  conf = HBaseConfiguration.create()
  hbase_admin = Hbase::Admin.new(conf, nil)
  hbase_admin.create(table_name, "demo")
end
```
