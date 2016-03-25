# Professional Scala development in Sublime Text

When I started programming in Scala, I started off in IntelliJ. Why shouldn't you? Everybody uses it, or at least my direct collegues at Xebia, and is has so many features, it is so powerful. Why would you ever want anything else?

What quickly surprised me was how much it was able to annoy me. It made my brand-new top-of-the-line MacBook feel like a seven-year-old budget laptop, even after increasing the heap and stack memory limitation to far beyond reasonable. And what's up with redefining every single shortcut instead of using the operation system's defaults?

Then I heard about some collegues using Sublime Text, I used it for some hobby projects in RoR, Python and PHP and absolutely loved it, so why not use it for Scala. It can't compare with IDE's on feature level, but it is such a joy to work in, you just can't not fall in love with it.

![Multiline editing][multiline-json]

> "What just happened? Did you just edit 1.700 lines simultaneously in a 40.000+ lines big json file?"

Well, 1.765 to be exact, but yes I did. This kind of thing is no big deal in Sublime land, and this is just barely touching the surface. The thing with Sublime Text is, it is way more powerful then you would imagine it to be, they just kind of hide it away.

## Time to begin


There are a few things you need to do to make Sublime Text even more awesome and suited for Scala, so let's get started.

### SBT

You need the [sbt-sublime][sbt-subl] plugin. Simply add/edit this file `~/.sbt/0.13/plugins/plugins.sbt` and add this line:

```sbt
addSbtPlugin("com.orrsella" % "sbt-sublime" % "1.1.1")


```
(Don't forget an empty line after each plugin).

Now in your project's root folder, add a new file `sublime.sbt`:

```sbt
sublimeExternalSourceDirectoryParent <<= baseDirectory { base =>
  base / ".sublime"
}

sublimeTransitive := true
```

Then in SBT, run the following command: `gen-sublime`.

This will create a Sublime Text project and download all dependencies (and their dependencies) into the freshly created .sublime folder in your project root. Why the .sublime directory? Well by default all dependencies are saved in the target folder, but you might empty that every now and then and downloading all dependencies over and over is a waste of your time.

### Useful plugins

Sublime Text has a rich plugin ecosystem. One of the most important plugins is Package Control, which is kind of the "apt-get of Sublime Text". Just follow [their instructions][subl-package], it's pretty easy.

After installation, you can use the built-in Command Palette which can be found by pressing `cmd+shift+p`. From there on, just fuzzy search for the command you want, like 'Package Control: Install Package' to install a single package.

Because we need to install several plugins, choose the 'Package Control: Advanced Install Package' and add these packages (comma-separated):

- [All Autocomplete][subl-allauto]
- [Case Conversion][subl-caseconv]
- [FileDiffs][subl-filediff]
- [Git][subl-git]
- [GitGutter][subl-gitgutter]
- [Pretty JSON][subl-prettyjson]
- [ScalaFormat][subl-scalaformat]
- [Sidebar enhancements][subl-sidebar]

> "Wow, wait a second Peter, what's all this?"

No worries, I'll explain why you need them.

**All Autocomplete:**

By default Sublime Text recognizes variables, classes and about everything else you type in your current document. So the next time you type it, it will try to autocomplete it for you. Pretty neat, but also a bit primitive as it is scoped to your current document only. All Autocomplete extends this range to all open tabs. Sublime Text does not understand Scala (or any other programming language) so can't intelligently suggest that someMethod is part of SomeClass and will always suggest someMethod if you type the first few letters. Therefore limiting the suggestions to all open tabs, instead of your entire project, is enough.

**Case Conversion:**

In case you often switch between camelCase and snake_case and back (a.k.a. writing Spray JSON Marshallers) or whatever other scenario you can imagine, you must have come to the point that this feels like something that you shouldn't do by hand. Well luckily for you it's only a little `ctrl+alt+c ctrl+alt+<letter of case you want>` away. Or just use the Command Palette (`cmd+shift+p`) and fuzzy search your case.

**FileDiffs:**

This might come as a total surpise, but this sweet plugin compares files. Nicely integrated in Sublime Text by rightclicking a tab or file in your sidebar and compare it to another file, open tab, or whatever else it is you fancy comparing.

**Git** and **GitGutter:**

Git brings you Git commands in your Sublime Text which might just come in handy every ones in a while. GitGutter shows you icons per line to indicate which lines have been inserted, modified or deleted.

**Pretty JSON:**

Open a JSON file, press `ctrl+cmd+j` and out comes beautifully aligned and highlighted JSON. And if it doesn't work, your JSON is invalid.

Trouble remembering the shortcut? Just fuzzy-search the command palette for 'Pretty JSON: Format (pretty print) JSON'.

**ScalaFormat:**

Format your Scala code using Scalariform by `ctrl+alt+f`, simple as that.

**Sidebar enhancements:**

Last but definitely not least, Sublime Text's context-menu (right mouse button) is pretty basic. 'Sidebar enhancements' brings just about everything you'd want from a sidebar, in your sidebar.

> "Man, I can't wait to start using Sublime Text now!"

Hold your horses, we're not there yet. Let's make your life even better with some minor Sublime Text tweaks, tips & tricks.


### Sublime tweaks, tips & tricks

**File jumping:**

By default Sublime Text 3 supports 'Goto Definition' which as the name says, goes to the definition of whatever it is your cursor is on. Simply press `cmd+alt+down` and if Sublime Text can find it, it will take you there. If it finds multiple hits, it will show you a the results in a list which you can simply quickview by pressing the up and down arrows.

Pretty slick, but how do you get back? Personally I don't like the default `ctrl+-` keybinding, and since you're unlikely to use the default behaviour of `cmd+alt+up`, let's override it. Via the menu, go to 'Sublime Text' > 'Preferences' > 'Key Bindings - User' or edit `~/Library/Application Support/Sublime Text 3/Packages/User/Default (OSX).sublime-keymap` (if you're in OSX) and add the following:

```json
[
  {
    "keys": ["super+alt+up"],
    "command": "jump_back"
  }
]
```

What did you say? You want to use the command + left mouse button to jump to definition, just like in IntelliJ? Well, that's just not gonna happen. Command clicking is you're gateway to the beautiful world of multiline editing. But no worries, how about a compromise with `alt+click`?

Simply create a new file named "Default (OSX).sublime-mousemap" next to the keymap file (`~/Library/Application Support/Sublime Text 3/Packages/User/Default (OSX).sublime-mousemap`) with the following:

```json
[
  {
    "button": "button1",
    "count": 1,
    "modifiers": ["alt"],
    "press_command": "drag_select",
    "command": "goto_definition"
  }
]

```

**Searching:**

There are a lot of ways to find what you're looking for:

- `cmd+p` will fuzzy search all files in your project (and dependencies).
- `cmd+r` will fuzzy search all classes, methods and variables in your current file.
- `cmd+shift+r` will fuzzy search all classes and methods in your project (and dependencies).

Still haven't found what you're looking for? `cmd+shift+f` will help you out. But before you do, it might become time to exclude some folders. Go to your product's '<projectname>.sublime-project' file and add the 'folder_exclude_patterns' line with the folders you want to exclude, I think this will be a good start.

```json
{
  "folders":[{
    "path":"/path/to/your/project",
    "folder_exclude_patterns":["target", ".tmp", ".idea", ".sublime"]
  },{
    "path":"/path/to/your/project/.sublime/External Libraries"
  }]
}
```

Now simply start searching, put "<project>" in the 'Where'-column to search your entire project. `cmd+alt+r` will toggle the Regex trigger when in search, in case you feel you need some more control over your searches.

**Paste history:**

Chances are you will never find this if nobody tells you, but Sublime Text remembers the last few things you copied, simply press `cmd+k+v` and Sublime Text will present you a dropdown with the last few clipboard items (which were copied in Sublime Text).

**Multiline editing:**

There are so many ways to multiline edit in Sublime Text, it is kind of impossible to not use it.

- `cmd+d` will either select your current word, or in case you already selected something, look for the next appearance of that selection and will select that as well.
- `ctrl+cmd+g` will also select your current word, or in case you already selected something, look for all appearances of that selection and will select those as well.
- `cmd+alt+leftmousebutton` will enable you to drag a selection and place a cursor on every line
- `cmd+shift+l` will put a cursor on every line you have selected.
- `cmd+l` will select the entire line where your cursor(s) is/are on.
- `cmd+leftmousebutton` will simply add another cursor.

And all of these get even better when you start combining them. Soon you'll wonder how you ever lived without it.

**Using file jumping for autocompletion:**

Time to get creative, say you have two files. 'horse.scala' containing your horse case class and all it's properties, and your brand new 'stable.scala'. You want to use the power of this fancy 'AllAutocomplete' plugin but you don't want to go through the hassle of double clicking the 'horse.scala' file in your sidebar and then navigating back to 'stable.scala'.

You don't have to. Once you typed 'Horse' in your 'stable.scala', simply `cmd+alt+down cmd+alt+up` to jump to the definition and jump right back where you were. This leaves the 'horse.scala' file open in your editor, therefore filling your autocomplete with all horse related fields and functions. If only everything in life was this easy..

## Getting a good workflow

Right now you might think, "How do I use this beautiful editor?" It comes down to personal preference of course, but what works for me is having a terminal on my second display running SBT.

When developing, which you obviously do TDD, simply automatically run the tests related to the code that you're refactoring everytime you hit save by `~testOnly path.to.code.AwesomeCodeSpec.scala`. Maybe use some wildcards `~testOnly path.to.code.*`.

Doing some massive refactoring? `~compile` will be your best friend.

Happy coding.

##Conclusion

I've been using Sublime Text for the last year non-stop, and honestly never looked back at IntelliJ a single time.
Yes, it's requires some more thinking, but once you figure out how to use (and combine) all those plugins, you will find yourself doing things you would have never thought of in your IDE.

![Creating a spray marshaller][multiline-marshaller]

Missing something in this post? Or have something to add to it? Let me know! Better yet, share it by posting a comment :)

Did you like this post? Check out [this blog][xebia-blog-scala-atom] about developing Scala in Atom!

[multiline-json]: json-multiline.gif "Multiline editing"
[multiline-marshaller]: spray-marshalling-multiline.gif "Creating a spray marshaller"

[sbt-subl]: https://github.com/orrsella/sbt-sublime
[subl-package]: https://packagecontrol.io/installation
[subl-allauto]: https://packagecontrol.io/packages/All%20Autocomplete
[subl-caseconv]: https://packagecontrol.io/packages/Case%20Conversion
[subl-filediff]: https://packagecontrol.io/packages/FileDiffs
[subl-git]: https://packagecontrol.io/packages/Git
[subl-gitgutter]: https://packagecontrol.io/packages/GitGutter
[subl-prettyjson]: https://packagecontrol.io/packages/Pretty%20JSON
[subl-scalaformat]: https://packagecontrol.io/packages/ScalaFormat
[subl-sidebar]: https://packagecontrol.io/packages/SideBarEnhancements

[xebia-blog-scala-atom]: http://blog.xebia.com/scala-development-atom-editor/
