---
layout: page
title: "Tales of the Refactoring #1"
date: 2015-09-15 22:09
comments: false
sharing: true
footer: true
---
###Brief explanation of 'tales of the refactoring'###

Tales of the Refactoring is a collections of interesting refactorings discovered while adding features, fixing bugs (unknown features), or just paying off some technical debt (features stashed for a later day). With that said know that some tales here may be misinterpreted, not fully understood, or just plain old wrong. Either way approach these tales with knowing that they are just explainations for a day of development.

These refactoring tales aren't meant to be ammunition for times when you are itching to refactor but more so  smells to be aware of while developing. Not to say that the smell is bad but just a reminder to step back and ask is this the design we want for this feature? (of course the specs gotta be green before you can step back :P) 

Action Items are meant for research for the author to understand what happened better and grow his ubiquitous language domain being Software Development.


###Configuration for conditional logic### 

While updating some validation logic we ran into a case where config options were passed in to allow conditional validation to happen. This wasn't an issue  until we needed another conditional validation to happen, then we quickly (ok maybe not so quickly and not we mainly my pair) relized that this isn't going to end well. Below is an example of some action class with config options to conditional do some action. 

~~~ruby
class ConfigAction
    def initialize(options = {})
        @switch_1 = options.fetch(:switch_1, false)
        @switch_2 = options.fetch(:switch_2, false)
    end
    
    def action 
        results = {}
        results.merge(switch_1_action) if switch_1       
        results.merge(switch_2_action) if switch_2
        results
    end
    
    private 
    
    attr_reader :switch_1, :switch_2
    
    def switch_1_action
        {switch_1: "result for switch_1 actions"}
    end
    
    def switch_2_action
        {switch_2: "result for switch_2 actions"}
    end
end
~~~

So when we need to add a new action say, switch_3 (beautiful name), we would need to have a new config option and another default for it and the new switch_3 action to the ConfigAction class.
My pair pointed out that it was looking like something he read from Martin Fowler's Refactoring 'Replace Conditional with Polymorphism' section. I should glance through that book and section one day

Long story short we turn that class into something like:

~~~ruby

class ConfigAction
    def initialize(options = {})
        @actions = options.fetch(:actions, [])
    end
    
    def action
        results = {}
        actions.each { |action| results.merge(action.action) }
        results
    end
    
    private
    
    attr_reader :actions
end
~~~

Actions now turn into their own objects that respond to `action`. The `ConfigAction` class no longer knows what switchs can activate actions, it doesn't even know what actions it can do. All it knows how to do is call `action` on some object given to it.   

The final product allows addictional actions to be added to the `ConfigAction` class when we choose to. We don't need to add behavior and extra tests for that behavior to the `ConfigAction` class when a new action is needed. This pays us back by not having to worry about the clients of `ConfigAction` and how their behavior might be effected by an additional action. The clients can define their own actions how they seem fit.

Action Items - research name of refactoring and patterns that would have arised.

###Decorated Algorithm###

Next we ran into this little gem while adding a new accessor.
(Note simplified example because I can't remember exactly how it was but it isn't the point to regurgitate what we did but to explained what was learned)

~~~ruby
class ComputerAccessor

  def computers
    Computers.all
  end
 
end

class MacCompterAccessor

  def computers
    ComputerAccessor.new.computers.where(type: 'Mac')
  end
end

class WindowsAndDellCompterAccessor

  def computers
    ComputerAccessor.new.computers.where
    (
      type: 'Windows',
      manufacturer: 'Dell'
    ) 
  end
end
~~~

After looking a the code puzzled and doodles describing the behavior of all the classes we encountered that decorated the `ComputerAccessor#computers` method.[^decorated] We came to the conclusion that all the enhanced `ComputerAccessor`'s were just filtering on either type or manufacturer. 

We ended up enhancing the original `ComputerAccessor` to look something like

~~~ruby
class ComputerAccessor
  def initialize( allowed_types: [], allowed_manufacturers: [])
    @allowed_types = allowed_typs
    @allowed_manufacturers = allowed_manufacturers
  end
    
  def computers
    Computers.all.where
    (
      type: allowed_types, 
      manufacturer: allowed_manufacturers
    )
  end
  
  private 
  attr_reader :allowed_types, :allowed_manufacturers
end
~~~

Action Items - need to conclude what we learned

Category Lesson's Learned

[^decorated]: Yes another new phrase learned today <q>Decorated Algorithm</q>

