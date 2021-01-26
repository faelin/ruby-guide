---
title: Ruby on Rails Training, pt. 1 – Ruby
description: Overview of the "Ruby" part of Ruby on Rails
tags: ruby, ruby on rails, coding, language, tutorial, learning
---

Ruby on Rails Training (pt. 1) – Ruby
===

## Table of Contents
> [TOC]

---
# Getting started with Ruby

---
## Variables Are Barewords

### Scope
```ruby
$global_var = "this has a global scope!"
value = 10
_private = nil
SPEED_OF_LIGHT = 299_792_458
```
| Begins         | Scope      | More Info |
|:---------------|:-----------|:----------|
| **$**          | *Global*   | Available everywhere within your ruby script. |
| **@**          | *Instance* | Available only within a specific object, across all methods in a class instance. Not available directly from class definitions. |
| **@@**         | *Class*    | Available from the class definition and any sub-classes. Not available from anywhere outside. |
| `[a-z]` or `_` | *Local*    | Availability depends on the context. You’ll be working with these most and thus encounter the most problems, because their scope depends on a variety of factors. |
| `[A-Z]`        | *Constant* | This is purely a naming convention, and is not enforced. |

---
## Built in Data Types
Unlike many languages, Ruby doesn't have primitive types; all Ruby data types are classes:

* Numbers
* Strings
* Symbols
* Arrays
* Hashes
* Nil
* True
* False

---
### Numbers
Ruby does not require you to distinguish between types of numbers. Instead, the result of any numeric operation is dependant on the types involved:
```ruby
puts 3 / 2
# => 1    [int / int = int]

puts 3.0 / 2
# => 1.5    [float / int = float]

puts 3 / 2.0
# => 1.5    [int / float = float]
```

---
### Strings
Unlike some other languages, all strings in Ruby are mutable, such that they can be modified in place without cloning the value.

Strings concatenate via the `+` operator — non-String types will *not* implicitly convert during concatenation.
```ruby
age = 12

puts "Today is day " + age
# => no implicit conversion of Integer into String (TypeError)

puts "Today is day " + age.to_s
# => Today is day 12
```
String interpolation via `#{}` forces implicit type conversion:
```ruby
## double-quoted strings attempt interpolation
puts "Today is day #{age} and still no word."
# => Today is day 12 and still no word.

## single-quoted strings are considered "literal"
puts 'Today is day #{age} and still no word.'
# => Today is day #{age} and still no word.
```

>> [color=green] *N.B. strings starting with the `%` string-indicator can be delimited by any character, not just quotes (meaning less need to escape special characters)! There exist a variety of string modifiers that change how the string is interpreted: `q,Q,i,I,w,W,r,x,s`*
```ruby
puts %$This is a string with "quotes" in it!$
# => This is a string with "quotes" in it!

value = "Thursday"

## %Q forces double-quoted context
puts %Q{Today is #{value}, but tomorrow is Friday!}
# => Today is Thursday, but tomorrow is Friday!

## %q forces single-quoted context
puts %q(Today is #{value}, but tomorrow is Friday!)
# => Today is #{value}, but tomorrow is Friday!
```

---
### Symbols
---

In Ruby, a *symbol* is a sort of immutable enumerated value. Consider the following code:
```csharp
enum Status { OPEN, CLOSED };
Status original_status = OPEN;
Status current_status  = CLOSED;
```
Because Ruby is a dynamic language, we don't need to declare a type `Status` nor define the values. Insteads we represent the enumeration values as *symbols*:
```ruby
original_status = :open
current_status  = :closed
```
Much like an enumeration, symbols are a form of reference value: any two symbols with the same name point to the same memory location!
```ruby
"string".object_id == "string".object_id
# => false

:symbol.object_id == :symbol.object_id
# => true
```

In Ruby (and Rails), we often use symbols as a sort of human-readable name:
```ruby
find_speech(:gettysburg_address)
```

>> [color=red] *N.B. Because of the way Ruby symbols are registered, they can never be garbage collected. In other words, if we create 10,000 one-off symbols that never get used again, the memory remains allocated for the duration of the runtime. For more info, see [this informative post](http://www.randomhacks.net/2007/01/20/13-ways-of-looking-at-a-ruby-symbol/).*

---
### Arrays
Arrays in Ruby are heterogeneous — they can contain multiple datatypes!
```ruby
arr = [1, "two", 3.0] # => [1, "two", 3.0]
```
To instantiate an array with a collection of values, a block can be passed to the array constructor:
```ruby
Array.new(4) { Hash.new }  # => [{}, {}, {}, {}]
Array.new(4) {|i| i.to_s } # => ["0", "1", "2", "3"]
```
Finally, Ruby provides several useful shortcuts to access Array valeus:
 * Array indexes that are out of bounds return nil.
 * Negative array indexes reference values right-to-left.
 * Two values separated by a comma will return a subset of array values determined by the offset and length arguments.
 * Finally, the range operator `..` can be used to select the values found at several indexes.
```ruby
arr = [1, 2, 3, 4, 5, 6]

arr[100]  #=> nil
arr[-1]   #=> 6
arr[-2]   #=> 5
arr[2, 3] #=> [3, 4, 5]
arr[1..4] #=> [2, 3, 4, 5]
arr[1..-3] #=> [2, 3, 4]
```
 
---
### Hashes (Ruby's Dictionaries)
Like Arrays, Hashes in Ruby can be heterogenous...and any object  can be used as a key (meaning that they aren't limited to integer or string datatypes).

>> [color=green] *Hashes derive their name from the hashing mechanism used to distribute their data across memory. For more information, see [this informative post](https://launchschool.com/blog/how-the-hash-works-in-ruby).*

A Hash can be easily instantiated via the `Hash.new` function, or by using the implicit form:
```ruby
grades = Hash.new
grades["Dorothy Doe"] = 9

grades = { "Jane Doe" => 10, "Jim Doe" => 6 }
```
>> [color=green] *The <code>=&gt;</code> symbol (known as the "fat comma" in many languages) is called a "Hash Rocket" in Ruby, and is used to indicate the relationship between a key/value pair in a Ruby hash declaration.*

Afterward, a value is accessed by using the associated key:
```ruby
puts grades["Jane Doe"] # => 10
```

Ruby 2.0.0 brought a new syntax shortcut for using symbols as keys, to bring Ruby hashes more in line with JSON stylization:
```ruby
:symbol = "some key symbol"
old_style = { :symbol => "zero" }
new_style =  { symbol: "zero" }

old_style[:symbol] == new_style[:symbol]
# => true
```

>> [color=green] *Hashes are also commonly used as a way to have named parameters in functions.*
>>
>> ```ruby
>> class Person
>>     def self.create(params)
>>         @name = params[:name]
>>         @age  = params[:age]
>>     end
>> end
>> 
>> body = Person.create(name: "John Doe", age: 27)
>> ```

---
## Operators, Functions, Methods

---
### Operators

![Operator Precendence Table](https://drive.google.com/uc?export=view&id=1gqiCP02zv8vujDIwd3YInqAq_QmSgHUK)

>> [color=green] *N.B. notable operators:*
>>  * `<<` — append (push) operator
>>  * `...`, `..` — range operators
>>  * `&&=`, `||=` — compound assignment
>>  * `<=>`, `=~`, `===` — equality and matching (RegEx)
>>  * `not`, `and`, `or` — verbose logical operators

---
### Parallel Assignment
Ruby also supports the parallel assignment of variables. This enables multiple variables to be initialized with a single line of Ruby code.
```ruby
a = 10
b = 20
c = 30

# this is equivalent:

a, b, c = 10, 20, 30
```

---
### Operators are Methods Too!
Many core operators are powered by underlying methods:
```ruby
## When you write
number = 2 + 3 * 4

array = [1, 2, 3]
array[2] = array[3]


## Ruby understands this as
number = 2.+(3.*(4))

array = [1, 2, 3]
array.[]=(2, array.[](3))
```

---
### Using Safe Navigation
The Safe Navigation operator `&.` can be used to avoid "NoMethod" errors. This is especially useful when calling a method on a variable that is assigned at runtime and could have a  `nil` value.
```ruby
account = Account.new(owner: nil)

account.owner.address
# => NoMethodError: undefined method `address' for nil:NilClass

account && account.owner && account.owner.address
# => nil

account&.owner&.address
# => nil
```
>> [color=#FF0000] *N.B. sadly, the Safe Navigation operator was only introduced in Ruby v2.3.0 and above.*

---
### The Versatile Splat Operator

>>> TODO

---
### Method Declarations
Methods are declared via a `def ... end` block. There are two types of arguments that can be declared in a signature: named, and unnamed. Unnamed arguments can be given default values via assignment in the signature. Named arguments are indicated by a `:` suffix, optionally followed by a default value.
```ruby
def raise(x, y = 1)
    return x ** y
end

def raise(value:, exp: 1)
    return :value ** :exp
end
```
Unnamed arguments can be made optional by a `*` suffix. Additionally, an unnamed-style signature can be made to "slurp" up all remaining arguments via a terminal splat operator:
```ruby
## optional arguments using '*'
def add(x, y*)
    if (y.nil?)
        return x
    else
        return x += y
    end
end

## "greedy" splat-signatures will slurp all remaining arguments
def add(x, *y)
    for i in y do
        x += i
    end
    
    return x
end
```
For named signatures, a `**` splat prefix will slurp all named arguments passed into a method, even if the names do not appear in the signature:
```ruby
def do_stuff(name:, **options)
    return options
end

do_stuff(name: "Zix", size: 9001, status: "very cool")
# => {size: 9001, status: "very cool"}
```
This is particularly useful for interacting with varying run-time input and NoSQL-type data storage.

---
### Return Values for Methods
All methods contain an implicit `return`. If you do not specify a return value, then the method will return the last evaluated expression.
```ruby
def increment(x)
    return x = x + 1
end

def double(y)
    y *= 2
end

num = 7

puts increment(num)
# => 8

puts double(num)
# => 16
```

---
### Predicate (Boolean) Methods
One common naming convention in Ruby is that any method that returns a boolean should end in a `?`:
```ruby
"foo".empty?
# => false

"".blank?
# => true
```

---
### Mutator ("Bang") Methods
Standard methods perform an action on a copy of the object and then return that copy.
```ruby
name = "Ruby Monsters"

puts name.downcase
# => ruby monsters

puts name
# => Ruby Monsters
```

> [maybe condense this?]

Bang methods perform an action directly on an object, returning the modified object as a result.
```ruby
name = "AppRiver + Zix"

puts name.downcase!
# => appriver + zix

puts name
# => appriver + zix
```
#### Use bang methods with caution! They are dangerous, and should generally be avoided.

---  
## Truthiness and Boolean Logic
![Truthiness Table](https://i.stack.imgur.com/qbe46.png)

---
# Flow Control and Blocks
## In Ruby nearly everything is an expression
```ruby
if num > 0
    puts "number is positive"
elsif num == 0
    puts "number is zero"
else
    puts "number is negative"
end


coin = if rand() > .5 then "head" else "tails" end


schedule = case day_of_week
when 'Sunday'
    puts 'Closed'
when 'Satusrday'
    puts 'Open 2-5'
else 
    puts 'Open 9-5'
end
```

---
## Block Structures
Blocks (sometimes known as anonymous functions) are code between braces `{}` or `do..end`.

The following statements are equivalent:
```ruby
for i in 0..9 do
    puts i
end

(0..9).each() { |i| puts i }

# compare to:
# (0..9).select(i => print i)
```

The synonyms `collect` and `map` are like the `map` function in most other languages.
```ruby
puts (0..9).collect { |i| i*i }
puts (0..9).map { |i| i*i }
# => [0,1,4,9,16,25,36,49,64,81]
```


These methods can also be chained:
```ruby
(0..9)
    .map { rand()} 
    .map { |num| if num > 0.5 then "head" else "tails" end } 
    .each { |coin| puts coin }
	
# (0..9).map {...}.map {...}.each{...}
```

The `select` function is like `filter` in most other languages:
```ruby
number_of_heads = (0..9).map { rand() } 
    .map { |num| if num > 0.5 then "head" else "tails" end }
    .select { |coin| coin == "head" }
    .count
```


`[].each do` block:
```ruby
for i in 0..9 do
    puts i
end

(0..9).each do |i|
    puts i
end

# C# style:
# (0..9).select(i => print i)
```


> [ SEE: https://www.geeksforgeeks.org/ruby-loops-for-while-do-while-until/ ]
> [ SEE: http://zetcode.com/lang/rubytutorial/flowcontrol/ ]


---
### Postfix statements
Conditional statements and even loops can be defined *after* a block, in a style known as "postfix".

```ruby
raise ArgumentError, 'Number cannot be less than 100' unless num > 100

puts i++ while i < 10
```

>> [color=green] *N.B. `unless ___` is the same as `if not ___`*


---  
## Exceptions
Ruby handles exceptions much like other languages; most useful exceptions derive from StandardError, and each Exception object is associated with a message string and a stack-trace.

Like everything in Ruby, exceptions are objects, and have an inheritance as follows:
```
Exception
   │
   ├── fatal   [used internally by Ruby]
   │
   ├── NoMemoryError
   ├── ScriptError
   │    ├── LoadError
   │    ├── NotImplementedError
   │    └── SyntaxError
   │
   ├── SecurityError
   ├── SignalException
   │    └── Interrupt
   │
   ├── StandardError
   │    ├── ArgumentError
   │    ├── StopIteration
   │    ├── IndexError
   │    │    ├── KeyError
   │    │    └── StopIteration
   │    │
   │    ├── IOError
   │    │    └── EOFError
   │    │
   │    ├── LocalJumpError
   │    ├── NameError
   │    │    └── NoMethodError
   │    │
   │    ├── RangeError
   │    │    └── FloatDomainError
   │    │
   │    ├── RegexpError
   │    ├── RuntimeError
   │    ├── SystemCallError
   │    │    └── [system-dependent exceptions (Errno::xxx)]
   │    │
   │    ├── ThreadError
   │    ├── TypeError
   │    └── ZeroDivisionError
   │
   ├── SystemExit
   └── SystemStackError
```

---
### Throw Catch
In keeping with Ruby tradition, the usual exception-handling terminology of `Try...Throw...Catch` has a slightly different meaning then you might expect.

Instead of error-handling, `Catch` blocks act as a sort of anonymous function, using `throw` to denote the return value:
```ruby
def index_of(target, list)
  index = catch(:index) {
    for pos in 0..list.length do
      if target == list[pos]
	    throw :index, pos
	  end
    end
    "NaN"
  }
  puts "#{target} in #{list} appears at position #{index}" unless index == "NaN"
end
```
This tortured example demonstrates the general (mis)use of `throw`; more often, a fully broken out function will be safer, easier to read, and more reusable than a `Catch` block.

---
### Begin Rescue
Instead of the usual `Try...Throw...Catch` block, exceptions are handled via `Begin...Raise...Rescue`:
```ruby
def raise_and_rescue  
  begin
    puts 'I am before the raise.'
    raise StandardError.new('An error has occured!')
    puts 'I am after the raise.'
  rescue OneTypeOfException => e
    puts "This is the message from an exception: #{e}"
  rescue AnotherTypeOfException => e
    puts "I am another rescue: #{e}"
  else
    puts 'I only print if no exception occured.'
  ensure
    puts 'I will always run, with or without an exception.'
  end
end
```

---
## Classes, Polymorphism, and Composition

---
### Classes and Inheritance
> [inheritance, private, attributes, `self`]

---
### Mix-in Modules
Modules are essentially static classes: they hold methods just like classes, but cannot be instantiated. This is useful if we have methods that we want to reuse in certain classes, but also want to keep them in a central place, so we do not have to repeat them everywhere. Modules can be utilized in a class via the `include` statement:
```ruby
class Chocolate
  include IceCream
end
class Vanilla
  include IceCream
end
module IceCream
  def flavor
  	...
  end
end

cone = Vanilla.new
puts cone.flavor
```
When a Module is injected into a class this way, the Module can be thought of as a sort of Superclass known as a "mix-in". This paradigm (composition over inheritance) is generally preferred by the Ruby community, to avoid leaky abstractions and overlapping inheritance.

---  
## Parenthesis
You will often see Ruby code that seems to be missing parenthesis. In Ruby, when you define or call (execute, use) a method, you can omit the parentheses entirely.

In fact, you have already seen us use the `puts` method this way throughout the presentation.
```ruby
puts("this has parens")
# => "this has parens"

puts "this does not"
# => "this does not"
```
There is no clear rule about this, but there are some conventions:

 - use parentheses for all method calls that take arguments, except for the methods puts and p, require, and include.
 - If a method does not take any arguments, then omit the empty parentheses.
```ruby
bad = Array.new()
good = Array.new

bad = "Missing parens?".concat " Don't do it."
good = "Parens".concat(" are the best!")
```

---
## Ruby Ecosystem

---
### Ruby Gems
Libraries and packages in Ruby are known as Gems, and are managed via the RubyGems package manager. Zix maintains its own Gem server that provides a number of proprietary, in-house gems, used throughout the Zix codebase. 

By default ruby gems are installed globally.

Gems are installed/removed via a simple command:
```bash
$ gem [un]install some_gem
```
---
### Bundler
Running applications that use different versions of dependencies can cause big problems because everything is installed globally. Bundler helps to manage dependencies. It gives us the `Gemfile`, `Gemfile.lock` and helps us to run commands in the "context" of our application.

---
### IRB
Ruby provides a REPL console environment called the Interactive Ruby Shell:
```bash
$ irb
```

When working within a Rails application, you should instead access the IRB via
```bash
$ bundle exec rails console
# Or just
$ bundle exec rails c
```

---
### Rake
Rake is a taskrunner. It does things like backup your database, run tests, and generate reports.

---

