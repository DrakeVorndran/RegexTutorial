---
title: "JS Regex Basics"
slug: js-regex-basics
---

# Regular Expressions
Before we tackle using regex in javascript, we should go over what regex even is.

Wikipedia "says Regular Expressions or "Regex" is a sequence of characters that define a search pattern."  
Which is accurate, but a little broad.  
My definition of Regex is a tool that allows you to be more general about searching strings. You do this searching by creating what a Regex "pattern" that describes what you are looking for.  
This may still be confusing, but lets just look at a small example first

Lets say you had an entire book as plain text, and you wanted to find where every chapter starts.  
If you were doing this through normal searching, you would search for something like `Chapter` and then click through all the results. This is good, but what if the word chapter was used somewhere in the book that wasn't just the start of a chapter. Well then you might start systematicly puting different numbers after Chapter `Chapter 1, Chapter 2...`. This works, but you have to manually type in all the numbers yourself.  
Well this is what regex can help with. With Regex, you can define a "pattern" That is a bit more general then just trying to match all of the text.  
`/Chapter [0-9]/`   
This is a simple regular expression that does almost exactly what we are looking for. So lets break this down.  
Starting off with the two forward-slashes that surround the entire expression. All those are doing are telling whatever language you are using, in this case javascript, that this is infact a regular expression.  
Moving on to the first part of the expression, `Chapter `. This works just like a normal search, trying to exactly match the word character for charecter, including the space. 
Alright the last part `[0-9]`  
Square brackets in regex are saying that anything within the brackets can be in this position. but it has some nice shortcuts such as `0-9` like we are using to repersent any single digit number, or `a-z` which says any lower case letter.  
However, regex also gives us `escaped characters` that allow us to do some of these with even less effort. for our `[0-9]` we can simply use `\d` which will give us any digit.  
So this regex pattern will match anything that first has the word `Chapter` followed by any number. Except for one small problem. This will find the location of any chapter, but if there are more then 9 chapters, the regex will only get the first number of that chapter. Is there a way around that, yes, another opperator regex gives us is the `at least one` opperator. This opperator is a simple `+` so the new pattern would look like `Chapter \d+` Which would match any text that had the word `Chapter` followed by at least one number, and will match any amount of numbers you put here.

This is just breaking the tip of the iceberg with regex, there are so many things you can do, and for a solid cheat sheet and an amazing regex playground that gives you explations of not only everything you can do with regex, but will also explain exactly what any pattern you put in is doing in great detail, go to [regexr.com](https://regexr.com/)

# Regex in Javascript

Ok So now we know what regex is and how to learn more about it, how do we actually use it javascript.

Well first off lets learn how to make one. JavaScript gives you two ways to make a regex pattern.  
By using the expression literal
```js
const re = /ac/;
```
Or by using the constructor function
```js
const re = new RegExp('ab+c');
```
They both end with the same result, use the one that is more semantic to you, I will be using the expression literal throughout this tutorial, mainly because in regex you use many forward slashes, and in js, the forward slash is an escape character in a string, so to actually use a forward slash you have to escape the forward slash with another forward slash, which gets confusing quickly and doubles the number of forward slashes needed in the pattern.

So what can you actually do with this. Well js has many helpful methods on strings that allow you to search through strings, and even replace parts of it using regex

Javascript has many helpful string methods that let you do things with regex. The first of many is the `string.match` method. This gives you the exact first match and the the first index
```js
const regex = /Chapter \d+/
const string = "Chapter 1, Chapter 2"
console.log(string.match(regex))
// returns
[ 'Chapter 1',
  index: 0,
  input: 'Chapter 1, Chapter 2',
  groups: undefined ]
```


Now this can be useful, but this will only give you the index of the very first match. For our chapters example however, we want every match.

Currently, while it is supported in browsers, `string.matchAll()` is not supported by node which is where you will most likely use it, but it returns the same structure as the array above, but with multiple entries. However you can use the global modifier to give the same result, which would turn our regex from before into `/Chapter \d+/g`

We can also seperate the entire book into its chapters with the `string.split()` method.  
This method allows you to split the string into an array with a string or regex. So if you wanted to split the entire string into each of it's words you could do `string.split(" ")` which would split the string on the space character, giving you each of the individual words. Or we could use our regex `string.split(/Chapter \d+/)` which will break up the book into it's chapters in a nice array for us.

Finally, we have `string.replace()` which will find and replace the first instance of the regex with a replacement string. Again if you use the global modifier by adding `g` after the closing backslash it will replace every instance not just the first
```js
var p = 'The quick brown fox jumps over the lazy dog. If the dog reacted, was it really lazy?';

var regex = /dog/g;

console.log(p.replace(regex, 'ferret'));
// expected output: "The quick brown fox jumps over the lazy ferret. If the ferret reacted, was it really lazy?"
```

Ok, now we've gone over a lot of the meat of using regex within javascript, let's apply it and build something.

If you have ever played Dungons and Dragons, or really most board games, you understand the importance of dice. There are many kinds of dice, and in games such as DnD They serve many roles. If you have ever used the online tool [roll20](roll20.net) which allows you to play DnD online with people, they have a really nice system for rolling dice in chat. they give you access to a command `/roll` which allows you to roll as many dice, with as many sides as you want. We are going to try and replicate this. So the actual command would look something like `/roll 5d6+3` We are going to try and read the `5d6+3` part with regex and roll the dice correctly. But first we should break down the input. What this is saying is to roll a 6 sided die 5 times and to add 3 to the total result. So the `d6` means the die is 6 sided, the `5` before that is the amount of rolls, and the `+3` is the modifier.  
So we actually need one more bit of regex knowlage before we should approach this. You may notice that eariler that in the find command the return provided us with `groups`. In regex you can put parentheses around part of the regex and that is a `group` that will be returned in that array. So first lets just try to match the regex with the roll.  

we know we need to start with the two back slashes `//`. Then lets go through each part of the input. So first off the number of dice. We can get this with `\d` But if the users just want it to be one die, they can leave that off and they could put in multidigit numbers we should add the `*` opperator which matches 0 or more, allowing them to leave it blank, or add as many as they want `\d*` and because we want to access this information when we find the match we should put it in a group `(\d*)`. Cool, So our total regex so far should look like
```js
const regex = /(\d*)/
```  
Next lets figure out what die we are using, so we know it starts with the letter d so `d`, and this is followed by a number, so `d\d` which is a little funky, but it means a d followed by any digit, but we need to make it any number of digits, so `d\d+` and finally we need to grab just the number in a group so `d(\d+)` so our regex so far looks like
```js
const regex = /(\d*)d(\d+)/
```  
Finally, they can add or subtract a value to the total, this one can be done in many ways, but we are going to keep it simple, so first we have to match either a plus or a minus, we can do that with a character set `[+-]` which which will match either a plus or minus. After that we need to match a digit so `[+-]\d` But again, we want it to be any number, not just one digit, so `[+i]\d+`. We also want the entire thing to be in a group so we can access it later `([+-]\d+)`. And finally we want this to be optional so `([+-]\d+)?` Making the entire regex
```js
const regex = /(\d*)d(\d+)([+-]\d+)?/
```  

Now we have our beautiful looking regex, lets build the code to make it roll dice, So to start, we need a function, and our regex

```js
function rollDice(inputString) {
  const regex = /(\d*)d(\d+)([+-]\d+)?/ // Our regex from before
}
```
Then we need to get each group out of the string, we can do this in a few ways, but we are going to use list destructuring. So first lets look at the data we get back when we ask for the match
```js
const regex = /(\d*)d(\d+)([+-]\d+)?/
const string = "5d6+3"
console.log(string.match(regex))
//expected ->
[ '5d6+3',
  '5',
  '6',
  '+3',
  index: 0,
  input: '5d6+3',
  groups: undefined ]

```
This is a little strange because it gives us groups, which doesn't seem to do anything, and it actually doesn't. What it actually gives you is an array starting with the entire match, then the first group the second and so on. So if you look, we have our entier match `'5d6+3'` followed by our first group, the number of rolls `5` and then the sides of the die, `6` and then the modifier `+3`

So we can use this, and destructure the array it gives us to get out the information we need

```js
function rollDice(inputString) {
  const regex = /(\d*)d(\d+)([+-]\d+)?/ // Our regex from before
  const [match, numRolls, faces, modifier] = inputString.match(regex) //Destructures the array, putting each value into its respective variable
  console.log(match, numRolls, faces, modifier)
}
rollDice('5d6+3')
// expected -> 5d6+3 5 6 +3
```

So now we need to actually right the function to roll the dice.

We should start with a function that rolls just a single die of any size
```js
function singleDie(sides) {
  let result = Math.random() //returns a random number from 0 to 1
  result *= sides //random number from 0 to the number of sides - 1
  result += 1 //random number from 1 to the number of sides
  result = Math.floor(result) //drops the decimal
  return result
}
```

Now we have our `singleDie` function, lets apply it to our other function,

```js
function rollDice(inputString) {
  const regex = /(\d*)d(\d+)([+-]\d+)?/ // Our regex from before
  const [match, rolls, faces, modifier] = inputString.match(regex) //Destructures the array, putting each value into its respective variable
  return singleDie(faces)
}
console.log(rollDice('5d6+3'))
// should be a random number between 1 and 6
```
Now we want to roll the die the right number of times

```js
function rollDice(inputString) {
  const regex = /(\d*)d(\d+)([+-]\d+)?/ // Our regex from before
  const [match, numRolls, faces, modifier] = inputString.match(regex) //Destructures the array, putting each value into its respective variable
  const rollArray = [] // Array to store the result of every roll
  for(let i = 0; i < numRolls ; i++ ) {
    rollArray.push(singleDie(faces)) // roll a new die and add the result to the roll Array
  }
  return rollArray
}
console.log(rollDice('5d6+3'))
```
