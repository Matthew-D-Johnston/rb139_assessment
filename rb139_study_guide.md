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
6. The method ends, which means the last expression's value is returned by this method. The last expression in the method invokes the `puts` method, so the return value for the methods is `nil`.

Good Use Cases for Writing a Method that Takes a Block:

* to defer some implementation code until method invocation.
* to let methods perform some kind of before and after actions.

**1. Deferring some implemenation code to method invocation decision:**  

* There are two roles involved with any method: the **method implementor** and the **method caller** (note that this could be the same person/developer). There are times when the method implementor is not 100% certain of how the method will be called. Maybe the method implementor is 90% certain, but wants to leave that 10% decision up to the method caller at method invocation time.  
* The method implementor can basically say, "hey, I don't know what the specifics of your scenario are, so just pass them in when you need to call this method. I'll set it up so that you can refine it later, since you understand your scenario better."
* Many of the core library's most useful methods are useful precisely because they are built in a generic sense, allowing us (the method callers) to refine the method through a block at invocation time.

**2. Methods that need to perform some "before" and "after" actions--sandwich code:**  

* Sandwich code is a good example of the previous point about deferring implementation to method invocation. There will be times when you want to write a generic method that performs some "before" and "after" action. Before and after what? That's exactly the point--the method implementor doesn't care: before and after _anything_. Let's explore an example.
* One area where before/after actions are important is in resource management, or interfacing with the operating system. Many OS interfaces require you, the developer, to first allocate a portion of a resource, then perform some clean-up to free up that resource. Forgetting to do the clean-up can result in dramatic bugs--system crashes, memory leaks, file system corruption. Woudln't it be great if we can automate this clean-up?

#### Blocks and variable scope

Take the following block line of code (which is shown without the method or calling object):

```ruby
         do |num|
	puts num
end
```

The `num` variable between the two `|`'s is a _parameter for the block_, or, more simply, a _block parameter_. Within the block, `num` is a _block local variable_. This is a special type of local variable where the scope is constrained to the block.  

It's important to make sure the block parameter has a unique name and doesn't conflict with any local variables outside the scope of the block. Otherwise, you'll encounter _variable shadowing_ (which we saw in the Programming Foundation course). Shadowing makes it impossible to access the variable defined in the outer scope, which is usually not what you want.  



#### Write methods that use blocks and procs



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

The `do...end` portion above is the block. The `num` variable between the two `|`'s is a _parameter for the block_, or, more simplyl, a _block parameter_. Within the block, `num` is a _block local variable_. This is a special type of local variable where the scope is constrained to the block.

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

---

### Testing With Minitest

#### Testing terminology

#### Minitest vs. RSpec

#### SEAT approach

#### Assertions

---

### Core Tools/Packaging Code

#### Purpose of core tools

#### Gemfiles