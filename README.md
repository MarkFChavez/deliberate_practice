==================================================================
RAILS ASSOCIATIONS
==================================================================

* To prevent nil, its best to do it on the database level. Here are some useful rails techniques to prevent this nil error:

1. New migration - the column can have null: false, default: '' (your preference)

2. Use the null object pattern. Awesome blog post http://devblog.avdi.org/2011/05/30/null-objects-and-falsiness/

Examples:

* User.find(1) || NullUser.new

class NullUser
def name
"no name"
end
end

* When doing cascade deletes, a good practice is to use ON DELETE restrict on the database level and dependent: :delete_all on the activerecord model.

* foreign key constraints are not used by rails by default. a workaround for this is to check out the "foreigner" gem. It adds foreign key helpers to migrations and correctly dumps foreign keys to schema.rb. Or a workaround which is "validates :foreign_key, uniqueness: true”

use the foreigner gem
access to methods like "add_foreign_key"

* In Rails, all association methods returns a collection proxy object that does the same as the original object.

* Most professional rails developers does not mind creating indexes for foreign keys at the time that those are created.

* In has many associations, you can add a collection using either the << array method or the create but there is a difference on their return values. 

- When adding an object via the << method, the return object will be the collection of the associated object

- When adding an object via the create! method, the return object will be the object created.

Gotcha: when adding a collection object, the before_add and after_add callbacks are called for the object


cool methods for has many associations

* to check if there are values for the collection, use the any? method

example: person.timesheets.any?

* to check if the association has more than 1 value, use the many? method

example: person.timesheets.many?

- For has many associations, when you execute delete or delete_all, the records will be nullified by default (foreign key value removed)

- You can also replace the current collection with a new one

* person.timesheets.replace(new_array)

- For belongs_to association, the association method accepts a "force_reload" parameter if you want to forcely query the database for the result. Normally, the result is cached.

* Example: person.subject(true)

- For belongs_to association, you can also add a counter_cache option to cache out the value of the count

* Example: on the person model add subjects_count column

on the subject model add counter_cache: true option

belongs_to :person, counter_cache: true

* The value of the subjects_count should default to 0

* For belongs_to association, you can pass the option "touch" to update the owning record's updated_at timestamp. This is mostly used to invalidate the cache.

* You can validate an association by using the validates_associated and pass in the association object

* Use the foreign_key option on both sides of the association

* The belongs_to association accepts an optional second block argument

Example: belongs_to :person, -> { where(approver: true) }

* You can use the includes command to eager load the association you have passed it

For example: Person.includes(:bags) - This query will load all the bags per person

* You can also set the association as readonly by:

Example: belongs_to :timesheet, -> { readonly }

* The convention for the has_many association is the singularize version

For example: has_many :timesheets refers to the timesheet model by default

* Callbacks you can add on a has many association are :

- before_add & after_add

For example: has_many :articles, before_add: -> (owner) { #do something }

- lambda does not check the arity of its parameters

* dependent: :delete_all - skips the callbacks defined for the model and just delete all the records

* dependent: :destroy - destroys each records and executes callbacks if there are one defined

* dependent: :nullify - does not destroy the child records, instead removes the foreign key value

* dependent: :restrict_with_exception - raises an exception when a parent object is destroyed which has child records


* for a has_many association, there is an option called inverse_of which will return the original instance. Commonly used for optimisation

For example: has_many :articles, inverse_of: :user, In this case article.user will be equal to the user.

has_and_belongs_to_many is obsolete. Its a good practice to use the has_many through for many to many relationships

To create a habtm relationship, (for example users and timesheets)

create_table :timesheets_users, id: false do |t| #there is no id on join table for a habtm relationship.

has_many through associations can be classified into two types: 

non-aggregating - these are the usual associations
aggregating - below is a good example:
  
For example:
      
class Grandparent
  has_many :parents
  has_many :grand_children, through: :parents, source: :children
          
class Parent
  belongs_to :grandparent
  has_many :children


===================================================================
COFFEESCRIPT
===================================================================

a programming language that compiles into javascript
syntax derived from ruby and python
created and maintained by jeremy ashkenas
syntax: clean and minimal
semicolons are not required
parenthesis are usually not required
curly braces are usually not required
commas are sometimes not required
uses indentation instead of {}
example:
    my_function = ->
          alert “hello world” #take note of the indentation

          creating a method
            hello_world = ->
                alert “hello world”

                creating a method with argument
                  sum = (first, second) ->
                      first  + second

                      creating an object
                        bill = 
                            name: “Mark”
                                age: 18

                                ALTERNATIVES TO COFFEESCRIPT
                                Traceur
                                ECMA Harmony/Narcissus
                                Script#
                                GWT
                                Pyjamas

                                In coffeescript, == is often replaced by ‘is’ (coffee script way)
                                Private methods in coffee script starts with ‘_’ (underscore)
                                the @ symbol means that it is an instance variable/method
                                Class example:
                                    class Value
                                          constructor: (@number) ->
                                              
                                                    map: ->
                                                            if @_is_fizzbuzz()

                                                            heredoc syntax for coffeescript. use an identifier like so
                                                            for example: name = ‘’’ this is my name ‘’’ #multiline

                                                            Avoid comments as possible
                                                            # for single line comment
                                                            ### this is a comment ### for multiline comment

                                                            Assigning multiple variables in one line
                                                            [a, b] = [1, 2]
                                                            also works with nested arrays
                                                            [a, [b, c], d] = [10, [5, 5], 30]
                                                            it also works with objects!
                                                            { name, address } = { name: “Mark”, address: “Philippines” }

                                                            Blocks in coffeescript (no curly braces required). Indentation is very important
                                                              if condition
                                                                  #some block
                                                                    else
                                                                        #another block

                                                                        if, else, unless are examples of conditionals
                                                                        you can use a one-liner
                                                                        example: say_hello() if condition
                                                                        there is no ternary operator in coffee script but here’s a workaround.
                                                                        account_colour = if balance < 0 then ‘red’ else ‘black’

                                                                        You can also do chained comparisons
                                                                        (a < b < c) - this will evaluate to (a < b && b < c)

                                                                        Loops
