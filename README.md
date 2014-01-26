next
====

"What should I do next?"

Is that a question you frequently ask yourself? Are you happy with the answers? Do you regularly make bad decisions about how to spend your time? Are there things that you know you should be working on, but you just keep overlooking? Do not worry, my friend. Help is at hand!

Configure this tool by writing an XML tree (yes, ugh, I know, but the data is heirarchical and XML is a great fit) that corresponds to the things you think you should be working on. (I'll add examples later, yo.) When you run this tool, it will filter the tree based on how you invoke it, and choose a single thing for you to work on. You can filter with xpath, or tags. The tree itself can be weighted!

Here's an example decision tree:

```
<root>
  <afternoon>
    <outside>
      <eat-stuff tags="hungry">
      <bike-ride>
      <walk>
    </outside>
    <inside>
      <eat-stuff tags="hungry">
      <chat-with-friends/>
      <computer-games weight="2"/>
    </inside>
  </afternoon>
  <evening>
    <eat-stuff tags="hungry">
      <eat-in/>
      <eat-out/>
    </eat-stuff>
    <movie>
    <watch-tv/>
    <chat-with-friends/>
    <computer-games weight="2"/>    
  </evening>
</root>
```

This is just an example. As you can see, I like chatting with friends, eating stuff, and playing computer games. (Obviously a bad example, as this is exactly the sort of stuff this tool was designed to help me avoid.) And I've chosen to order my activites by time of day. But you can choose any system you like. Perhaps you'd prefer to organise it by location, or context, or someething else!

_@@ Remove the eating example. It doesn't make any sense to configure time for eating!_

Some example queries:

```
next.py //afternoon/inside
```

This asks for a task based on the xpath selection of `afternoon/inside`. We'll have a 50% chance of playing computer games, and a 25% chance of eating stuff or chatting with friends.

Note: elements were originally designed to encode context or groupings. So, for example, you might have one set of tasks that you want to pick from when you at at the office, and another when you at home. Or you might have one set on weekdays, and another on weekends.

Similarly, being able to group tasks into a heirarchy allows you to assign weights in a more isolated fashion. Perhaps on an evening, you have two primary focuses: input and ouput. (For example, reading blog posts v.s writing blog posts.) You could then say that you want to spend 60% of your time on output, and 40% of your time on input. But then _within_ those groupings, you can have more groupings, and assign weights to them without worring about how those weights effect weights in other parts of the tree.


```
next.py //evening +hungry
```

This asks for a task based on the xpath selection of `evening` matching the tags `hungry`. With our example configuration, we'll have a 50/50 chance of eating in or eating out.

Note: tags were originally designed to encode mood or energy levels. So you can tag some tasks as being suited for a certain energy level. When you are in that mood, or have that energy, you can specify that when requesting a new task. In this way, while the system is probabilistic, the tasks assigned can be suited to your cognitive state.

@@ This was originally written as a command for an IRC bot, so the command syntax is a little weird for a CLI tool. I plan to refactor this.

Synax:

```
next.py FLAGS XPATHS TAGS
```

FLAGS can be:

```
:stats
```

Print outcome probability stats for your selection (helpful for debugging, so that you know your configuration is doing what you expect it to do!)

```
:tags
```

Print the tags that apply to your selection (helpful for debugging your configuration, or just getting a sense for what's available)

XPATHS can be:


```
/whatever
```

Use this to specify a part of your configuration tree. Can chain as many as you like. Only nodes that can be reached from parts of the tree you select will be available for selection

TAGS can be:

```
+foo
```

Restrict selection to nodes that have the `foo` tag

```
-bar
```

Restrict selection to nodes that do not have the `bar` tag

@@ These docs are incomplete and need improving!

General idea:

- dishes out new activities
- random weighted decision tree
- .tree choose
- .dm (dungeon master) (or does it stand for decision matrix? hehe)
- random time table
- random periods

I don't like the current name and want to pick a new one.

Name ideas:

- coin flip
  - plenty of cultural currency (har har) in this idea already
  - living your life by the flip of a coin
- taskmaster
- task rotation system
  - stochastic task rotation system (strs)
- activity roster
- activity dice
- activity cards
- roll
- deal

Functionality:

tree of tasks, and you can specify weightings and tags at any level
so if top nodes are A and B at 50% each, then the algo chooses one of them first, before descending to child nodes and doing the same again
search query can include or exclude tags, +/-
update scripts:

go through file and look for "id" notes, and if it matches, update the sub-weight or whatever
anything that does not fit gets put into an uncategorised section with a weight of zero, or an alert is sent
config formats:

can i assemble the json from a spreadsheet? something that gives me a table view to edit with. perhaps even sucking directly from a google doc?
is there a visual json editor?
