##### RB139 Assessment: Ruby Foundations More Topics > Instructions for Assessment RB139

---

## Study Guide for Test

### Blocks

#### Closures and Scope

A **closure** is a general programming concept that allows programmers to save a "chunk of code" and execute it at a later time. It's called a "closure" because it's said to bind its surrounding artifacts (i.e. variables, methods, objects, etc.) and build an "enclosure" around everything so that they can be referenced when the closure is later executed.  

It's sometimes useful to think of a closure as a method that you can pass around and execute, but it's not defined with an explicit name. Different programming languages implement closures in different ways.  

In Ruby, a closure is implemented through a `Proc` object, a lambda, or a block. That is, we can pass around these items as a "chunk of code" and execute them later. 

Some properties of **closures**:

* lets you save a chunk of code and execute it later.
* you can pass closures around like objects.
* closures do not need explicit names.
* closures can be created by passing a block to a method, creating a `Proc` object, or creating a lambda.

A closure creates a binding:

* a closure retains access to variables, constants, and methods that were in scope at the time and location you created the closure. It binds some code with the in-scope items.
* Proc objects keep track of their surrounding context, and drag it around wherever the chunk of code is passed to. In Ruby, we call this its **binding**, or surrounding environment/context. A closure must keep track of its surrounding context in order to have all the information it needs in order to be executed later. This not only includes local variables, but also method references, constants and other artifacts in your code--whatever it needs to execute correctly, it will drag all of it around.

Closures drag their surrounding context/environment around, and this is at the core of how variable scope works.

#### How blocks work, and when we want to use them

Blocks are arguments that are passed to methods at method invocation time. For example, take the following method call on an Array object:

```ruby
[1, 2, 3].each { |num| puts num }
```

The block bit of code, `{ |num| puts num }`, is actually an argument that is passed to the `Array#each` method.  

The `yield` keyword allows users of a method to come in after the method has been fully implemented and inject additional code in the middle of this method (without modifying the method implementation), by passing in a block of code.  

The following is an example of a simple method definition that takes a block, and a breakdown of how the method functions when invoked with an implicit block argument:

```ruby
 1 | # method implementation
 2 | def say(words)
 3 | 	yield if block_given?
 4 | 	puts "> " + words
 5 | end
 6 |
 7 | # method invocation
 8 | say("hi there") do 
 9 | 	system 'clear'
10 | end
```

Breakdown of code execution:

1. Execution starts at method invocation, on line 8. The `say` method is invoked with two arguments: a string and a block (the block is an implicit parameter and not part of the method definition).
2. Execution goes to line 2, where the method local variable `words` is assigned the string `"hi there"`. The block is passed in implicitly, without being assigned to a variable.
3. Execution continues into the first line of the method implementation, line 3, which immediately yields to the block.
4. The block, line 9, is now executed, which clears the  screen.
5. After the block is done executing, execution continues in the method implementation on line 4. Executing line 4 results in output being displayed.
6. The method ends, which means the last expression's value is returned by this method. The last expression in the method invokes the `puts` method, so the return value for the method is `nil`.

Good Use Cases for Writing a Method that Takes a Block:

* to defer some implementation code until method invocation.
* to let methods perform some kind of before and after actions.

**1. Deferring some implemenation code to method invocation decision:**  

* There are two roles involved with any method: the **method implementor** and the **method caller** (note that this could be the same person/developer). There are times when the method implementor is not 100% certain of how the method will be called. Maybe the method implementor is 90% certain, but wants to leave that 10% decision up to the method caller at method invocation time.  
* The method implementor can basically say, "hey, I don't know what the specifics of your scenario are, so just pass them in when you need to call this method. I'll set it up so that you can refine it later, since you understand your scenario better."
* Many of the core library's most useful methods are useful precisely because they are built in a generic sense, allowing us (the method callers) to refine the method through a block at invocation time.

**2. Methods that need to perform some "before" and "after" actions--sandwich code:**  

* Sandwich code is a good example of the previous point about deferring implementation to method invocation. There will be times when you want to write a generic method that performs some "before" and "after" action. Before and after what? That's exactly the point--the method implementor doesn't care: before and after _anything_. Let's explore an example.
* One area where before/after actions are important is in resource management, or interfacing with the operating system. Many OS interfaces require you, the developer, to first allocate a portion of a resource, then perform some clean-up to free up that resource. Forgetting to do the clean-up can result in dramatic bugs--system crashes, memory leaks, file system corruption. Wouldn't it be great if we can automate this clean-up?
* Blocks are great for wrapping logic, where you need to perform some before/after actions.

#### Blocks and variable scope

Take the following block line of code (which is shown without the method or calling object):

```ruby
         do |num|
	puts num
end
```

The `num` variable between the two `|`'s is a _parameter for the block_, or, more simply, a _block parameter_. Within the block, `num` is a _block local variable_. This is a special type of local variable where the scope is constrained to the block.  

It's important to make sure the block parameter has a unique name and doesn't conflict with any local variables outside the scope of the block. Otherwise, you'll encounter _variable shadowing_ (which we saw in the Programming Foundation course). Shadowing makes it impossible to access the variable defined in the outer scope, which is usually not what you want.  

A block creates a new scope for local variables, and only local variables are accessible to inner blocks.

#### Write methods that use blocks and procs

* Write an `each` method.
* Write a `reduce` method.
* Remember that blocks are a form of `Proc`.

#### Methods with an explicit block parameter

Every method, regardless of its definition, takes an _implicit_ block. It may ignore the implicit block, but it still accepts it.  

However, there are times when you want a method to take an explicit block; you do that by defining a parameter prefixed by an `&` character in the method definition, as in the following example:

```ruby
def test(&block)
  puts "What's &block? #{block}"
end
```

The `&block` is a special parameter that converts the block argument to what we call a "simple" `Proc` object.  Notice that we drop the `&` when referring to the parameter inside the method.  

But why do we need an explict block anyways?  

Chiefly, the answer is that it provides additional flexibility. Before, we didn't have a handle (a variable) for the implicit block, so we couldn't do much with it except yield to it and test whether a block was provided. Now we have a variable that represents the block, so we can _pass the block to another method_.  

Ruby converts blocks passed in as explicit blocks to a simple `Proc` object (this is why we need to use `#call` to invoke the `Proc` object).

#### Arguments and return values with blocks

Block Parameters and Arguments:

* You can pass more arguments than the block parameter list shows.
* You can pass fewer arguments than the block parameter list shows. The omitted arguments will be `nil`.
* The rules regarding the number of arguments that you can pass to a block, `Proc`, or `lambda` in Ruby is called its _arity_. In Ruby, blocks have lenient arity rules, which is why it doesn't complain when you pass in different number of arguments; `Proc` objects and `lambda`s have different arity rules.

Example of a block that is defined with a parameter:

```ruby
3.times do |num|
  puts num
end
```

The `do...end` portion above is the block. The `num` variable between the two `|`'s is a _parameter for the block_, or, more simply, a _block parameter_. Within the block, `num` is a _block local variable_. This is a special type of local variable where the scope is constrained to the block.

Blocks and Method Return Values:

* Sometimes blocks will affect the return value of the method and sometimes not; it all depends on the method implementation, that is on how the method was defined.
* It's completely up to the method implementation to decide what to do with the block of code given to it.

The Return Value of a Block:

* Blocks have a return value, and that return value is determined based on the last expression in the block.
* Just like normal methods, blocks can either mutate the argument with a destructive method call or the block can return a value.

#### When can you pass a block to a method

Methods that take blocks:

* Any Ruby method can take a block.
* In Ruby, every method can take an optional block as an implicit parameter. You can just tack it on at the end of the method invocation.
* If the method is defined with explicit parameters, then the number of arguments passed explicitly to the method at method invocation needs to match the exact number of explicit parameters in the method definition, regardless of whether we are passing in a block.
* The `yield` keyword in the method definition/implementation is what executes the block.
* If a method is implemented with a `yield` keyword, then a block must be passed as an argument to the method in order to avoid a `LocalJumpError`, unless the `yield` is conditional on the return value of the `Kernel#block_given?` method. For example, `yield if block_given?` will only attempt to execute a block line of code if a block line of code has been passed to the method.

#### `&symbol`

If you look closely, somehow this code:

```ruby
(&:to_s)
```

...gets converted to this code:

```ruby
{ |n| n.to_s }
```

The mechanism at work here is related to the use of `&` with explicit blocks, but since it isn't applied to a method parameter, it's also different. Let's break down the code: `(&:to_s)`. First, when we add a `&` in front of an object, it tells Ruby to try to convert this object into a block. So two things are happening:

* Ruby checks whether the object after `&` is a `Proc`. If it is, it uses the object as-is. Otherwise, it tries to call `#to_proc` on the object, which should return a `Proc` object. An error will occur if the `#to_proc` fails to return a `Proc` object.
* If all is well, the `&` turns the `Proc` into a block.

Let's pause here and look again at `(&:to_s)`. This means that Ruby is trying to turn `:to_s` into a block. However, it's not a Proc; it's a Symbol. Ruby will then try to call the `Symbol#to_proc` method--and there is one! This method will return a `Proc` object, which will execute the method based on the name of the symbol. In other words, `Symbol#to_proc` returns a `Proc`, which `&` turns into a block, which turns our shortcut into the long form block usage.  

A lone `&` applied to an object causes Ruby to try to convert the object to a block. If that object is a proc, the conversion happens automagically. If the object is not a proc, then `&` attempts to call the `#to_proc` method on the object first.

---

### Testing With Minitest

#### Testing terminology

The type of testing covered in this lesson, if we must call it anything, call it _unit testing_.  

**Minitest:** 

* Ruby's default testing library.
* Reads just like normal Ruby code.
* Uses a straight forward syntax.
* Unlike RSpec, Minitest is not a DSL.

**Test Suite:**  

* This is the entire set of tests that accompanies your program or application. You can think of this as _all the tests_ for a project.  
* The term can be used quite loosely: a test suite can test an entire class, a subset of a class, or a combination of classes, all the way up to the complete application. The test suite may be entirely in one file, or it may be spread out over several files.

**Test:**

* This describes a situation or context in which tests are run.
* Tests are instance methods whose names are prepended by `test_`.
* A test can contain multiple assertions.

**Assertion:**

* This is the actual verification step to confirm that the data returned by your program or application is indeed what is expected.
* You make one or more assertions within a test.
* They are what we are trying to verify.
* Examples of assertions: `assert`, `assert_equal`, `assert_raises`, etc.

`require 'minitest/autorun'`

* Loads all the necessary files from the `minitest` gem.

`require_relative 'name_of_file_to_be_tested'`

* Allows us to access the file containing the code that we wish to test.

`< MiniTest::Test`

* Appended to the test class that we are defining (i.e. we need to subclass from `MiniTest::Test`).
* Allows our test class to inherit all the necessary methods for writing tests.

`test_`

* This is what must prepend the name of all our instance methods used for testing.
* It is a naming convention used by Minitest; method names that are prepended with `test_` let Minitest know that these methods are individual tests that need to be run.
* Within each test we will need to make some _assertions_.

**"seed"**

* Tells Minitest what order the tests were run in.
* Most test suites have many tests that are run in random order.
* The "seed" is how you tell Minitest to run the entire test suite in a particular order.
* Rarely used, but can be helpful when you have an especially tricky bug that only comes up when certain specific situations come up.

**`.`, `S`, or `F`**

* `.` is an indication that a particular test passed.
* `S` is an indication that a particular test was skipped (tests can be skipped by placing the `skip` keyword at the beginning of a test).
* `F` is an indication that a particular test failed.

**regression tests**

* Regression tests check for bugs that occur in formerly working code after you make changes somewhere in the codebase.
* Using tests to identify bugs means we don't have to verify that everything works manually after each change.

**code coverage**

* A measure of how much of a program is tested by a test suite.
* Can be used as a metric to assess code quality.

#### Minitest vs. RSpec

Though many people use RSpec, Minitest is the default testing library that comes with Ruby. From a pure functionality standpoint, Mintiest can do everything RSpec can, except Minitest uses a more straight forward syntax. RSpec bends over backwards to allow developers to write code that reads like natural English, but at the cost of simplicity. RSpec is what we call a **Domain Specific Language**; it's a DSL for writing tests.  

We use Minitest because it reads just like normal Ruby code, without a log of magical syntax. It's not a DSL, it's just Ruby.

Minitest also has a completely different syntax, called _expectation_ or _spec-style_ syntax, which mimics RSpec's syntax. In expectation style, tests are grouped into `describe` blocks, and individual tests are written with the `it` method. We no longer use assertions, and instead use `_expectation matchers_`. 

Example:

```ruby
require 'minitest/autorun'

require_relative 'car'

describe 'Car#wheels' do
  it 'has 4 wheels' do
    car = Car.new
    car.wheels.must_equal 4						# this is the expectation
  end
end
```

Minitest comes installed with Ruby; RSpec doesn't.

#### SEAT approach

In larger projects, there are usually 4 steps to writing a test:

1. Set up the necessary objects.
2. Execute the code against the object we're testing.
3. Assert the results of the execution.
4. Tear down and clean up any lingering artifacts.

Note: both the `setup` and `teardown` methods are called for each and every test; that, is the `setup` method will be called before running every test, and the `teardown` method will be called after running every test.  

In the simplest cases, we won't need either set up or tear down, but just keep in mind that there are 4 steps to running any test, and it is SEAT. At the minimum, you'll need EA, even if the E is just a simple object instantiation.

Including Set Up and Tear Down steps reduces redundancy in the Test Suite code.

#### Assertions

* Each assertion is a call to a method whose name begins with `assert`.
* Assertions test whether a given condition is true.

**Some popular assertions:**    

`assert(test)` : Fails unless `test` is truthy.  

`assert_equal(exp, act)` : Fails unless `exp == act`.  

`assert_nil(obj)` : Fails unless `obj` is `nil`.  

`assert_raises(*exp) { ... }` : Fails unless block raises one of `*exp`.  

`assert_instance_of(cls, obj)` : Fails unless `obj` is an instance of `cls`.  

`assert_includes(collection, obj)` : Fails unless `collection` includes `obj`.  

**Refutations:**  

* The opposite of assertions.
* They _refute_ rather than _assert_.
* Every assertion has a corresponding refutation. For example, the opposite of `assert` is `refute`. `refute` passes if the object you pass to it is falsey.
* Refutations all take the same arguments, except it's doing a _refutation_.
* And yes, there is a `refute_equal`, `refute_nil`, `refute_includes`, etc.

`assert_equal`

* When we use `assert_equal`, we are testing for _value equality_. Specifically, we're invoking the `==` method on the object. If we're looking for more strict _object equality_, then we need to use the `assert_same` assertion.

---

### Core Tools/Packaging Code

#### Purpose of core tools  

**RubyGems (i.e. Gems)**

* Packages of code that you can download, install, and use in your Ruby programs or from the command line.
* The `gem` command manages your Gems; all versions of Ruby since version 1.9 supply `gem` as part of the standard installation.
* There are thousands of Gems, including:
  * `rubocop`: checks for Ruby style guide violations and other potential issues in your code.
  * `pry`: helps debug Ruby programs.
  * `sequel`: provides a set of classes and methods that simplify database access.
  * `rails`: provides a web application framework that simplifies and speeds web applications development.
* Some other things to remember:
  * Rubygems provide a library of code that you can download and run or use directly inside your Ruby programs. You use the `gem` command to manage the Gems you need.
  * Rubygems also provide the mechanisms you need to release your own Gems, which can either be packages of code you `require` into your Ruby programs, or independent Ruby programs that you can run (e.g., the `bundle` program from the Bundler gem).
  * Ruby projects usually use the Rubygems format.

**Ruby Version Managers**  

* Programs that let you install, manage, and use multiple versions of Ruby.
* Different versions of Ruby have different version-specific features and thus being able to use and manage differnt versions of Ruby is important for being able to take advantage of different features.
* Another reason to use Ruby version managers is when working on multiple applications. Software applications tend to standardize on a specific Ruby version in order to guarantee developers don't use unsupported language features. Thus, you will need the assistance of a Ruby version manager to help you manage and move between different Rubies as you switch between different projects.
* Two major ruby version managers in common use: RVM and rbenv.

**Bundler**  

* Bundler is a dependency manager.
* Dependency managers allow developers to manage Gem dependencies, helping developers to manage different versions of specific Gems required for their projects.
* Ruby version managers can be used to manage different versions of Ruby as well as different versions of Gems, but developers tend to prefer a dependency manager for Gem versions.
* The most widely used dependency manager in the Ruby community, by far, is the Bundler Gem. This Gem lets you configure which Ruby and which Gems each of your projects need.
*  Bundler lets you describe exactly which Ruby and Gems you want to use with your Ruby apps. Specifically, it lets you install multiple versions of each Gem under a specific version of Ruby and then use the proper version in your app.
* To use Bundler, you provide a file named `Gemfile` that describes the Ruby and Gem versions you want for your app. You use a DSL described on the Bundler website to provide this information.
* Bundler uses the `Gemfile` to generate a `Gemfile.lock` file via the `bundle install` command. `Gemfile.lock` describes the actual versions of each Gem that your app needs, including any Gems that the Gems listed in `Gemfile` depend on. The `bundler/setup` package tells your Ruby program to use `Gemfile.lock` to determine which Gem versions it should load.  
* The `bundle exec` command ensures that executable programs installed by Gems don't interfere with your app's requirements. For instance, if your app needs a specific version of `rake` but the default version of `rake` differs, `bundle exec` ensures that you can still run the specific `rake` version compatible with your app.
* Bundler provides the tools you need to describe the dependencies for your Ruby programs. This makes it easy to distribute your program to other systems: Bundler installs all the necessary components, and even ensures that the program uses the correct version of each Gem.

**Rake**  

* A Rubygem that automates many common functions required to build, test, package, and install programs; it is part of evey modern Ruby installation, so you don't need to install it yourself.
* Here are some common Rake tasks that you may encounter:
  * Set up required environment by creating directories and files.
  * Set up and initialize databases.
  * Run tests.
  * Package your application and all of its files for distribution.
  * Install the application.
  * Perform common Git tasks.
  * Rebuild certain files and directories (assets) based on changes to other files and directories.
* In short, you can write Rake tasks to automate anything you may want to do with your application during the development, testing, and release cycles.
* Rake provides a way to easily manage and run repetitive tasks that a developer needs when working on a project.
* Rake uses a file named `Rakefile` that lives in your project directory; this file describes the tasks that Rake can perform for your project, and how to perform those tasks. Below is an example of a very simple `Rakefile`:

```ruby
desc 'Say hello'
task :hello do
  puts "Hello there. This is the `hello` task."
end

desc 'Say goodbye'
task :bye do
  puts 'Bye now!'
end

desc 'Do everything'
task :default => [:hello, :bye]
```

* This `Rakefile` contains three tasks: two that simply display a single message, and one task that has the other tasks as prerequisites or dependencies. The first two tasks are named `:hello` and `:bye`, while the final task is the **default** task; Rake runs the default task if you do not provide a specific task name when you invoke Rake.  

* One reason why you need Rake is that nearly every Ruby project you can find has a `Rakefile`, and the presence of that file means you need to use Rake if you want to work on that project.  

* While you can always opt-out of using Rake in your projects, there is little point to doing so. Every project that aims to produce a finished project that either you or other people intend to use in the future has repetitive tasks the developer needs. For instance, to release a new version of an existing program, you may want to:  

  * Run all tests associated with the program.
  * Increment the version number.
  * Create your release notes.
  * Make a complete backup of your local repo.

`.gemspec`

* A file that provides information about a Gem. If you decide to release a program or library as a Gem, you must include a `.gemspec` file.

**Relationships between the Core Tools**

* Your Ruby Version Manager is at the top level––it controls multiple installations of Ruby and all the other tools.
* Within each installation of Ruby, you can have multiple Gems––even 1000s of Gems if you want. Each Gem becomes accessible to the Ruby version under which it is installed. If you want to run a Gem in multiple versions of Ruby, you need to install it in all of the versions you want to use it with.
* Each Gem in a Ruby installation can itself have multiple versions. This frequently occurs naturally as you install updated Gems, but can also be a requirement; sometimes you just need a specific version of a Gem for one project, but want to use another version for your other projects.
* Ruby projects are programs and libraries that make use of Ruby as the primary development language. Each Ruby project is typically designed to use a specific version (or versions) of Ruby, and may also use a variety of different Gems.
* The Bundler program is itself a Gem that is used to manage the Gem dependencies of your projects. That is, it determines and controls the Ruby version and Gems that your project uses, and attempts to ensure that the proper items are installed and used when you run the program.
* Finally, Rake is another Gem. It isn't tied to any one Ruby project, but is, instead, a tool that you use to perform repetitive development tasks, such as running tests, building databases, packaging and releasing the software, etc. The tasks that Rake performs are varied, and frequently change from one project to another; you use the `Rakefile` file to control which tasks your project needs.

#### Gemfiles

* The Bundler Gem relies on a file named `Gemfile` to tell it which version of Ruby and its Gems it should use. This file is a simple Ruby program that uses a Domain Specific Language (DSL) to provide details about the Ruby and Gem versions. It's the configuration or instructrion file for Bundler.
* Bundler uses a file named `Gemfile` to determine the dependencies of your project. The dependencies specified in this file will let other developers know how to run your project.
* After you create `Gemfile`, the `bundle install` command scans it, downloads and installs all the dependencies listed, and produces a `Gemfile.lock` file. `Gemfile.lock` shows all the dependencies for your program; this includes the Gems listed in `Gemfile`, as well as the Gems they depend on (the dependencies), which may not be explicitly listed in the `Gemfile`. It's very common for RubyGems you install for use in your project to rely on many other gems, creating a large dependency tree.
* A `Gemfile` typically needs four main pieces of information:
  * Where should Bundler look for Rubygems it needs to install?
  * Do you need a `.gemspec` file?
  * What version of Ruby does your program need? (Recommended, not required).
  * What Rubygems does your program use?
* Here is a simple example: suppose you are writing a program that requires Ruby 2.3.1 and the `sinatra`, `erubis`, and `rack` Gems. Our `Gemfile` incorporates these dependencies, and looks like this:

```ruby
source 'https://rubygems.org'

ruby '2.3.1'
gem 'sinatra'
gem 'erubis'
gem 'rack'
gem 'rake', '~>10.4.0'
```

* After creating this file, run the `bundle install` command.
* Note that the above code is all Ruby code: the `Gemfile` uses a DSL supplied by Bundler.
* Some of the Gems listed in our `Gemfile` may have their own dependencies and thus their own `Gemfile`. That means that when we run the `bundle install` command, not only will the Gems we specify in the `Gemfile` be installed but all the Gems that those Gems depend on will also be installed. By recursively scanning the `Gemfile`s for each Gem, Bundler builds a complete dependency list that identifies all the Gems your applications needs, even those you don't know you need. Once Bundler has the dependency list, it installs any Gems that are not yet part of your Ruby installation.  
* `bundle install` also creates a `Gemfile.lock` file that contains all the dependency information for your app:

```
GEM
  remote: https://rubygems.org/
  specs:
    ansi (1.5.0)
    builder (3.2.4)
    minitest (5.14.0)
    minitest-reporters (1.4.2)
      ansi
      builder
      minitest (>= 5.0)
      ruby-progressbar
    ruby-progressbar (1.10.1)

PLATFORMS
  ruby

DEPENDENCIES
  minitest (~> 5.10)
  minitest-reporters (~> 1.1)

RUBY VERSION
   ruby 2.5.0

BUNDLED WITH
   2.1.4
```

* When you update your `Gemfile`, you should rerun `bundle install` to force an update to `Gemfile.lock`.
* We must also tell each Ruby program in our project to use Bundler. To do this, add the following `require` to all your main program files (`lib/todolist_project.rb` and `test/todolist_project_test.rb`):

```ruby
require 'bundler/setup'
```

* You must require `bundler/setup` before any other `require` statements. It helps ensure that your `Gemfile` is complete; if you try to load a Gem that isn't in the `Gemfile`, `bundler/setup` prevents Ruby from finding the file.



