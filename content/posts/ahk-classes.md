# AHK Classes
Classes in AutoHotkey 2 provide a way to organize variables and functions in your code. They do this by creating new types of structures, known as class objects, that let you store information and functionality in one place. 

## What is a Class?
A class is nothing more than a container that holds variables and functions that are closely associated. This container gives you the ability to look in just one place to make changes. This helps you not repeat yourself and save time scrolling through your code to look for related items.

A class is a blueprint. It sets out internal variables--called attributes--and functions--called methods--for the class and the class alone to be able to use. When we use a class in the main part of our script, we create a whole new object based on the class.  This is called an instance, which is based on the blueprint. Just like with the blueprint of a house, which can be used to create many identical houses,  we can make many different instances of our class in our code.

### Example: Inkjet Printer
Let's look at a simple example: an inkjet printer. Printers are real objects that use software. They have a variety of attributes and states, such as brand names and models, power (on/off), and ink levels. They perform actions like printing.  You can perform actions on the printer, like turning it on and off, changing the paper size, and unjamming paper.

To start, we name our printer class using the keyword 'class' and giving the class a name, 'InkJetPrinter'. 

```autohotkey
	class InkJetPrinter {
	
	}
```

There it is, our new class. But it's empty and won't be very useful as a blueprint if we don't give it more life. Let's give it some attribues (internal variables) so that it is useful. To do this, we need to set the value of these attributes. Let's start like this, which is intuitive:
```python

	class InkJetPrinter {
	    power := "ON"
	    status := "Ready"
	}

```
Once we have these attributes set, we can create an instance. Let's call our printer instance 'inky.' Use the assignment operator, just like any other object, and don't forget the closing parens. After that, we can customize the object with unique attributes:
```python

	inky := InkJetPrinter() 
	inky.model := "HP1000"
	inky.brand := "HP"
	inky.paper_size := 'letter'

```
### Dot Notation For Attributes
To get to the attribute values that are in the class instance, we use what's called "dot notation" to 'drill down' into an attribute. It's written in the form _object.attribute_. We can put our old friend msg box in front of it to show the value: 
```python

	msgbox inky.power
	msgbox inky.paper_size

```
And there we go! We get a read out of both values. 

## Constructors
But hang on! Do we have to define unique attributes one by one with each instance? In fact, there is a special method for AHK classes called '\_\_New'. It's the constructor method. It takes arguments, allowing you to customize your instance in one line. Here's how works to add brand name, model number, power, paper size, and ink levels, all at once
```python

class InkJetPrinter {
	__New(brand, model) {
	    this.brand := brand
	    this.model := model
	    this.power := "ON"
	    this.paper_size := "letter"
	    this.ink_levels := Map("cyan",100,"yellow",100,"magenta",100,"black",100)
	}
}

```
Let's break down what we did. We added the method "\_\_New" to the class. The constructor runs every time a new instance is created. When we turn on a physical inkjet printer, certain settings are there by default: that the power is on, that the paper size is set to a certain size—like letter—and what the ink levels are.  When we say:
```python

	this.power := "ON"
	this.paper_size := "letter"
	this.ink_levels := Map("cyan",100,"yellow",100,"magenta",100,"black",100)

```
…we're doing that for our inkjet printer.

## Constructor Parameters
Not all printers are alike. There are many different brands and a variety of models. We don't want to create all of our printers alike, so  we can pass in some information for the "\_\_New" method to use. These constructor parameters allow us to customize things on instantiation. Here's how we do this:
```python

	__New(brand, model) {
	    this.brand := brand
	    this.model := model
	...}

```
## What's ‘this'?
You probably noticed that our variable names begin with "this." When we create instance variables for our printer, we need to do it in a special way because they belong to the class.  To do this, we use the keyword ‘this' to show that they belong to the InkJetPrinter class (this class!) and not another one. These variables are available to the class, but not outside the class unless used along with the instance object. 

At first, this looks puzzling. this.model := model? These names are very similar and can initally be confusing. What is going on here? To clear this up, remember that we need to make these variables available to the whole class. The only variables that the whole class can see must have "this" before the variable name, otherwise they're local to the method and invisible to the rest of the class. So we're taking the local parameters (brand, model) and putting their information into the "this" instance variables. 

## Methods
The printer now has some instance variables to work with, but it still doesn't do very much.  Let's add some things for it to do, like print some pages for us. 
```python

class InkJetPrinter {

	__New(brand, model) {
	    this.brand := brand
	    this.model := model
	    this.power := "ON"
	    this.paper_size := "letter"
	    this.color_levels := Map("cyan",100,"yellow",100,"magenta",100,"black",100)
	}
	
	Print(pages){
	    msgbox "Printed " pages " page(s)."
	}
}

```
Excellent! We've add a new method called Print that takes one argument--the number of pages--and returns a message to the user that a certain number of pages were printed. To see how this works in a script, start a new script called printer.ahk. Add the code above.  Then, below that, create a new instance of the printer. We'll keep the name 'inky' from before. 
```python

	; OUR MAIN SCRIPT
	inky := InkJetPrinter("HP","HP1000")

```
### Dot Notation For Method Calls
Let's tell it to print 10 pages using the Print method. This is different from a function call that we learned about before, because we call the mathod on an instance. To do this, you name the instance first 'inky', add a dot '.' and then name the method and any parameters: 'Print(10)'
```python

	inky.Print(10)

```
You should then see a message box with: "Printed 10 page(s)." We just created an instance in just one line and got it to print! 

## Methods Can Use And Change Instance Variables
Excellent! We just printed 10 pages! However, all that printing likely used up some ink.  Let's make sure that's captured in the Print method. We can do that very easily by adding a couple of lines to the Print method's code. To make it simple, let's say that every page uses up one unit of ink of each color (yes, that's a lot of ink!). We can write:
```python

	Print(pages) {
	    for color, level in this.color_levels {
	        this.color_levels[color] -= (pages * 1)
	    }
	    msgbox "Printed " pages " page(s)."
	}

```
(You'll remember our for-loop structure and how we iterate over map objects from previous chapters.)

Let's check the ink level directly in our code. For example, let's look at the Cyan level. 
```python

	; OUR MAIN SCRIPT
	inky := InkJetPrinter()
	inky.Print(10)
	msgbox inky.color_levels["Cyan"] ; => 90

```
## Keeping Track Of The Printer's State
One of the more powerful things about classes is that they allow us to keep track of our class objects in an elegant, self-contained way.  We can check things about it--its state--easily, and change it quickly.  For example, let's write a new method called ShowInkLevels so that we can see everything at once and not have to do it manually each time:
```python

	ShowInkLevels() {
	    result := "CURRENT LEVELS:`n"
	    for color, level in this.color_levels{
	        result .= color " -> " level "`n"
	    }
	    msgbox result
	}
	
	; OUR MAIN SCRIPT
	inky := InkJetPrinter()
	inky.Print(10)
	inky.ShowInkLevels() ; => msgbox, 90 for all levels

```
We can check any of the instance variables with this approach. The last thing we will do is to add an About method that informs the user of the current overall state of the printer.
```python

	About() {
	    current_state := this.brand "/" this.model "`nPower: " this.power "`nPaper Size: " this.paper_size
	    msgbox current_state 
	}

```
## Calling Other Instance Methods Within The Class
Finally, let's consider what happens if our ink levels get too low in the printer. The printer should probably warn the user by checking the ink level after every print and sending a message if the level on any one ink tank is below 10. To do that, we can adapt our method--ShowInkLevels--to the new context.  We want to preserve the ability to call it at any time, but also provide a special warning if the ink is low. 

Let's add a sentinel to our Print statement, warn\_low\_ink, and set it to false. Because we're already looping through the color\_levels map, we can check after every use with:
```python

	if this.color_levels[color] < 10
		warn_low_ink := true

```
Then we need to add in our method, ShowInkLevels.  Because it's being used inside the class, we need to use the "this." keyword so it will work properly. 
```python

	if warn_low_ink {
	    msgbox "INK LEVELS LOW"
	    this.ShowInkLevels()
	}

```
### Try It Yourself
Now it's your turn to do a few exercises to solidify your knowledge.  See if you can do these three challenges to expand the InkJetPrinter class.

1. Implement a toggle method to toggle the printer off and on.  Remember to change the status of the printer to "OFF" as well.
2. Implement a method to change the paper size.
3. For fun, add a random element to Print to make the printer jam.  Then, write a method to unjam the printer called FixMyPrinter.
4. The warning for the low ink when printing could be better.  Add to the existing methods and (a) combine the LOW INK WARNING with the ink levels message and (b) make sure to tell the user specifically which ink level is low. 

## Summary
In this chapter, we talked about classes being an excellent tool for keeping your code organized. We defined what they were, and built one called InkJetPrinter. We used AHK's built-in constructor method, \_\_New(), to set the printer up, and added a few constructor parameters with the new keyword, "this." to to set everything up.  Then, we built three methods: Print, About, and CheckInkLevels, to add functionality to the printer, and learned how to use one method inside another one. 