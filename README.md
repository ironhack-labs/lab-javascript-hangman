![](https://i.imgur.com/1QgrNNw.png)

# JS | Hangman

## Learning Goals

After this lesson, you will be able to:

## Introduction

In this learning unit, we are going to create the classic game Hangman. In this game, we have to figure out which is the secret word before the man gets hanged. With each error, the man will be closer to die!

![](https://i.imgur.com/wrQrY1T.png)

As we have learned, we can't confront the whole game without splitting it up in different steps to make the solution. We will split it up in three different parts: structure, logic, and game layout.

## Structure

To do the game, we will need four different data structures to play the game. This structures are the following:

- Words. An array with some words. We will take a random word from this array, and it will be the word that the player will have to figure out.
- Secret word. This will be an string to store the word selected randomly.
- Letters. Another array, in this case just for letters. This one will store all the letters used by the player, to show them all. It will be very helpful for the player, to avoid select the same letter twice.
- Errors left. Number of errors that the user can make until the end of the game.

All these variables will be defined in the constructor of the game, as it follows:

```javascript
function Hangman () {
  this.words      = ["IRONHACK", "NODEJS", "JAVASCRIPT", "METEOR", "ANGULAR"];
  this.secretWord = "";
  this.letter     = [];
  this.errorsLeft = 10;
}
```

Those are all the data structures we need to play the game. The next step is to create the game logic with all the methods we need.

## Game logic

### Get random word

The first thing we need to play the game is the word that the player will have to discern. We need to choose a random word and assign it to the `secretWord` variable we declared in the constructor.

```javascript
Hangman.prototype._getWord = function () {
  var random = Math.floor(Math.random() * this.words.length);
  return this.words[random];
};
```

We will execute this function in the constructor of the game, and assign it to the correct variable:

```javascript
function Hangman () {
  this.words      = ["IRONHACK", "NODEJS", "JAVASCRIPT", "METEOR", "ANGULAR"];
  this.secretWord = "";
  this.letters    = [];
  this.errorsLeft = 10;

  this.secretWord = this._getWord();
}
```

All right, we have the word, now the player should be able to indicate a letter to play.

### Insert a letter

The user should be able to indicate a letter that he thinks that the word contains. We have to create a function, that we will call `askLetter` that will receive as a parameter the user's selection. Once we receive the letter, we have to add it to the `letters` array, update the errors left (if the letter is not in the word), and we have to check out if the letter had been inserted before:

```javascript
Hangman.prototype.askLetter = function (letter) {
  letter = letter.toUpperCase();

  if (this.letters.indexOf(letter) > -1) {
    return;
  }

  this.letters.push(letter);
  var correct = this.secretWord.indexOf(letter) > -1;

  if (!correct) {
    this.errorsLeft -= 1;
  }

  return correct;
};
```

We use capital letters to compare the player's selection with the secret word. **Use capital (or lower) case letter to compare strings is a good practice.**

### Show the current word

The following method we need will be used to show the current status of the word. In other words, how many letters have to figure out the player. This method will be called `getWordStatus`, and it will be as it follows:

```javscript
Hangman.prototype.getWordStatus = function () {
  var that        = this;
  var wordStatus  = [];
  var splitedWord = this.secretWord.split("");

  splitedWord.forEach(function (letter) {
    if (that.letters.indexOf(letter) > -1) {
      wordStatus.push(letter);
    } else {
      wordStatus.push("_");
    }
  });

  return wordStatus;
};
```

We iterate over all the letters of the secret word. If a letter hasn't been selected by the player, it will be represented as an underscore. By doing this, we will get a result similiar to: "IRON_ACK".

### Game status

To finish up the game logic, we will need to create a method to get its status. We will have three different status:

- Completed.
- Game over.
- Ongoing game.

We will create three different functions:

```javascript
Hangman.prototype._isFinished = function () {
  return this.getWordStatus().indexOf("_") < 0;
};
```

If the current word doesn't have any underscore, the word will be completed. So this function will help us to determine if the player has won the game. We also need a function to figure out if the game is over:

```javascript
Hangman.prototype._gameOver = function () {
  return this.errorsLeft === 0;
};
```

With the variable we defined in the constructor it's very easy to know if the game is over or not. Note that both functions are private, so we have to create a public method to check out those possible status:

```javascript
Hangman.prototype.gameStatus = function () {
  if (this._isFinished()) {
    return "You Win";
  } else if (this._gameOver()) {
    return "Game Over";
  }
};
```

This function will return three possible values: "You Win", if the player has won, "Game Over" if the player has lost, and finally, undefined if the game has to go on.

We already have all what we need to play our game. The next step is to create the layout and put them all together.

## Game Layout

### HTML & CSS

First of all, we are going to create the layout with HTML and CSS. After that, we will create the JavaScript that will join the game logic with its layout. Let's do a list of all the things we need to do the layout:

- New game. We will add a button to start a new game. It will be always available to allow the user restart a game if the word is too complicated.
- Hangman. We need, of course, to draw the man to let the player know how much life left he has.
- Word. We need some kind of container to show the user the current status of the word.
- Letters. We also need another container to let the user know which letter have been already inserted.
- Win/Lose. Last, but not least, we need some kind of container to let the user know if he has won or not.

#### New game

First of all we will create the new button game. It will be in the top part of the page. We will add customized styles, without using any kind of library like Bootstrap. The HTML, CSS, and result of this button is the following:

**HTML**

```htmlmixed
<button id="new-game">
  New game
</button>
```

**CSS**
```css
#new-game {
  background: #43a3e6;
  border: 1px solid #43a3e6;
  border-radius: 6px;
  box-shadow: 1px 1px 2px #999;
  color: #fff;
  padding: 6px 14px;
  outline: 0;
}
```

**Layout**

![](https://i.imgur.com/cZ3SJPH.png)

Note that the font is not the default browser's font. We have imported the 'Open Sans' font from [Google Fonts](https://fonts.google.com/). You can find several font types in this directory :)

#### Hangman

We will draw the hangman with HTML and CSS. A lot of people is usually afraid of doing something similar to that in HTML and CSS. Let's prove that it's easier than it seems:

**HTML**

```htmlmixed
<div id="hangman">
  <div class="right-bar"></div>
  <div class="top-bar"></div>
  <div class="left-bar"></div>
  <div class="base"></div>

  <div class="head"></div>
  <div class="body"></div>
  <div class="left-arm"></div>
  <div class="right-arm"></div>
  <div class="left-leg"></div>
  <div class="right-leg"></div>

  <div class="clearfix"></div>
</div>
```

You can see how we have created a `<div>` for each part of the hangman. Each of these parts represent a life, so we will have to find a good way to show them all, depending on the status of the game. Let's take a look at the CSS and the layout:

```css
#hangman div {
  background: #000;
}

#hangman .base {
  height: 2px;
  width: 300px;
}

#hangman .left-bar {
  height: 400px;
  width: 2px;
}

#hangman .top-bar {
  height: 2px;
  width: 200px;
}

#hangman .right-bar {
  float: left;
  height: 30px;
  margin-left: 200px;
  width: 2px;
}

#hangman .head {
  background: none;
  border-radius: 100%;
  float: left;
  margin-left: 175px;
  margin-top: -375px;
  height: 50px;
  width: 50px;
}

#hangman .body {
  float: left;
  height: 140px;
  margin-top: -321px;
  margin-left: 200px;
  width: 2px;
}

#hangman .left-arm,
#hangman .right-arm,
#hangman .left-leg,
#hangman .right-leg {
  float: left;
  height: 90px;
  margin-top: -321px;
  margin-left: 173px;
  transform: rotate(35deg);
  width: 2px;
}

#hangman .right-arm,
#hangman .right-leg {
  margin-left: 230px;
  margin-top: -324px;
  transform: rotate(140deg);
}

#hangman .left-leg,
#hangman .right-leg {
  margin-top: -192px;
}
```

As you can see, we are using the div background color to draw it. To show the arms and legs, we are rotating the containers, and we play a lot with the margins to allocate all the pieces on their correct position. The result will is the following:

**Layout**

![](https://i.imgur.com/V4xwngn.png)

As we said, we will have to show each part depending on how many lifes does the player have. We will solve that by doing setting up the default background color as white. We will also create classes for each life, and depending on how many lifes he has, we are going to change the background color and set it up to black:

```css
#hangman div { background: #fff; }

/* ... */

#hangman.lifes-10 .base     { background: #000; }
#hangman.lifes-9 .left-bar  { background: #000; }
#hangman.lifes-8 .top-bar   { background: #000; }
#hangman.lifes-7 .right-bar { background: #000; }
#hangman.lifes-6 .head      { border: 2px solid #000; }
#hangman.lifes-5 .body      { background: #000; }
#hangman.lifes-4 .left-arm  { background: #000; }
#hangman.lifes-3 .right-arm { background: #000; }
#hangman.lifes-2 .left-leg  { background: #000; }
#hangman.lifes-1 .right-leg { background: #000; }
```

The only class that is not changing tha background color is the `.head` class, that will change the border-color, because we are using a borde-radius to make it circular. We will have to add the classes to the `#hangman` container through JavaScript during the game. We will see this later.

#### Word container

The following step is to add the container where we will place the word that has to be discerned. We have to think in the usability of this container: we have to separate the letters between them to let the player know how many letters compose the word:

**HTML**

```htmlmixed
<div id="currentWord">
  <span>I</span>
  <span>R</span>
  <span>O</span>
  <span>N</span>
  <span>H</span>
  <span>A</span>
  <span>C</span>
  <span>K</span>
</div>
```

We will use an `<span>` for each letter in the word. The CSS for this section is the following:

**CSS**

```css
#currentWord span {
  display: inline-block;
  margin: 0 10px;
}
```

The container by itself doesn't have an specific style, but we add some spacing between `<span>` through margin property.

**Layout**

The resulting layout is the following:

![](https://i.imgur.com/5GnF5Su.png)

#### Letters container

We also need another container to indicate which letters have already been used. It will be very similar to the current word container, but the font color will be different to distingish between the word and the letters.

**HTML**

```htmlmixed
<div id="letters">
</div>
```

**CSS**

```css
#letters {
  color: #999;
  letter-spacing: 5px;
  margin: 20px auto 0;
  text-align: center;
  width: 400px;
}
```

**Layout**

![](https://i.imgur.com/CF6Dbhv.png)

#### Win/Lose

Finally, we have to add the containers to show the win/lose message. It has no secret, we just have to create the container, add the message and stylize them.

**HTML**

```htmlmixed
<div id="you-win">
  <p>Good job, you saved our man!</p>
</div>

<div id="game-over">
  <p>Game over!</p>
</div>
```

**CSS**

```css
#you-win,
#game-over {
  background: #87D892;
  border: 2px solid #407847;
  border-radius: 6px;
  color: #407847;
  margin: 40px auto 0;
  text-align: center;
  width: 300px;
}

#game-over {
  background: #F9C3B9;
  border-color: #8A2512;
  color: #8A2512;
}
```

**Layout**

![](https://i.imgur.com/CQnF27k.png)

#### CSS details

Okay, let's analyze what we already have:

![](https://i.imgur.com/lPZDFa5.png)

We have to do the following things before start coding our JavaScript:

- Center all the content in the screen
- Hide the elements we don't need to start the game

To do the first thing, we will create a container and add some styles to it:

**HTML**

```htmlmixed
<div id="main">
  <button id="new-game">
    New game
  </button>

  <!-- ... -->
</div>
```

**CSS**

```css
#main {
  margin: 40px auto;
  width: 600px;
}
```

On the other hand, we will create a class to hide the elements we don't need to start the game. With JavaScript, we are going to add and hide the elements by adding and removing this class:

**HTML**

```htmlmixed
<div id="game" class="hide">
  <div id="currentWord"></div>
</div>

<div id="letters">
</div>

<div id="you-win" class="hide">
  <p>Good job, you saved our man!</p>
</div>

<div id="game-over" class="hide">
  <p>Game over!</p>
</div>
```

**CSS**

```css
.hide {
  display: none;
}
```

### JavaScript

The last part of the game is to join both the game logic and the game layout. We are going to add all the functionality in a file called `application.js`. We have to add both files in the correct order in the HTML, just before the closing tag `</body>`.

```htmlmixed=46
<script src="js/hangman.js"></script>
<script src="js/application.js"></script>
```

We will have to create, more or less, the same functionality that we have created in the layout. Let's do it.

#### New game

First of all we will declare a global variable to access to the game functions. This variable will be initialized every time that the "New game" button is clicked.

```javascript=
var hangman;

var newGameClick = function () {
  hangman = new Hangman();
};

document.getElementById("new-game").addEventListener("click", newGameClick);
```

We are also attaching the click event to the button, so it will execute the `newGameClick` function. Which is the next thing we have to do? Show the user the word he has to discern.

#### Draw current word

We will create a function to draw the current word. It will take the word from the `hangman` global variable, and add it into the container we created in the layout. It will be executed when we create a new game.

```javascript=3
var newGameClick = function () {
  hangman = new Hangman();
  drawCurrentWord();
};

var resetCurrentWord = function () {
  var word = document.getElementById("currentWord");

  while (word.firstChild) {
    word.removeChild(word.firstChild);
  }
};

var drawCurrentWord = function (word) {
  resetCurrentWord();

  var currentWord    = word || hangman.getWordStatus();
  var currentWordDom = document.getElementById("currentWord");

  currentWord.forEach(function (letter) {
    var spanLetter = document.createElement("span");
    var content    = document.createTextNode(letter);

    spanLetter.appendChild(content);
    currentWordDom.appendChild(spanLetter);
  });
};
```

We have also created a function that will reset the current word container before draw the new status. The following we need is to create a function that will wait for the player to insert a letter to the game.

#### Insert letter

There are several ways to insert the letter to check out if it's in the secret word. We could have a form where the player inserts a letter and press a button to send it. Another option is to add an event to the document to capture any press key, and send it to the hangman. We will do the second option.

We will create the `insertLetter` method that will be triggered every time a key is pressed on the document. We have to do several things:

- We don't want to execute the event when **any** key is pressed. We just want to capture the event when a letter is pressed.
- If the selected letter is not in the word, we have to add it to the used letters list.
- After receive the letter, we have to render the current word, the current hangman status, and check out if the game has finished.

Let's go step by step, first of all let's create the method and attach it to the "keyDown" event of the document.

```javascript
var insertLetter = function (event) {
};

document.addEventListener("keydown", insertLetter);
```

You can check out the [keydown documentation](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent) to see the received parameter. Through this parameter we can access to the pressed key, with the `event.keyCode` property, that will give us the letter ASCII code.

```javascript
var insertLetter = function (event) {
  if (event.keyCode < 65 || event.keyCode > 90)
    return;
};
```

The following step is to check out if the inserted letter is contained in the secret word:

```javascript
var insertLetter = function (event) {
  if (event.keyCode < 65 || event.keyCode > 90)
    return;

  var letter  = String.fromCharCode(event.keyCode);
  var correct = hangman.askLetter(letter);
};
```

[`String.fromCharCode`](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/String/fromCharCode) returns a string created by using the ASCII representation of the same. Remember that the `hangman.askLetter` method returns true if the letter is in the secret word, false if it's not, and undefined if the letter was already inserted. We can use this value to add the used letter if it's not correct, and to draw the current status of the word if it exists:

```javascript
var insertLetter = function (event) {
  if (!hangman || (event.keyCode < 65 || event.keyCode > 90))
    return;

  var letter  = String.fromCharCode(event.keyCode);
  var correct = hangman.askLetter(letter);

  if (correct !== undefined && !correct) {
    _addLetter(letter);
  } else {
    drawCurrentWord();
  }
};
```

We will add the wrong letter just if it is not in the container yet. We also have to draw the hangman if the letter is incorrect. We haven't defined this method yet, so it could be the next one:

#### Draw hangman

```javascript
var drawHangman = function () {
  document.getElementById("hangman").classList += " lifes-" + (hangman.errorsLeft + 1);
};
```

We are going to add each `lifes-*` class to the `hangman` container, so it will appear a piece of picture with each error. We just have to call it when the letter is incorrect:

```javascript
var insertLetter = function (event) {
  if (!hangman || (event.keyCode < 65 || event.keyCode > 90))
    return;

  var letter  = String.fromCharCode(event.keyCode);
  var correct = hangman.askLetter(letter);

  if (correct !== undefined && !correct) {
    _addLetter(letter);
    drawHangman();
  } else {
    drawCurrentWord();
  }
};
```

To finish up this method, we just need to check out if the game has finished. We will create an `afterRound` function to do that, and it will be called at the end of the `insertLetter` method:

#### After round

```javascript
var afterRound = function () {
  var status = hangman.gameStatus();

  if (!status)
    return;

  if(status.toLowerCase() === "you win") {
    document.getElementById("you-win").classList = "";
  } else {
    drawCurrentWord(hangman.secretWord.split(""));
    document.getElementById("game-over").classList = "";
  }

  hangman = undefined;
};
```

If the game is not finished, the status will be undefined. On the other hand, if its finished, it will return an string to indicate if the player has won or lost. We also set the `hangman` variable to undefined to avoid game interactions once it's finished.

If the player has lost the game, we should show him the secret word he couldn't discern. We do that by calling the `drawCurrentWord` method, indicating as a parameter the secret word splitted up by characters. Now we will add the call to the `afterRound` method after each round:

```javascript
var insertLetter = function (event) {
  if (!hangman || (event.keyCode < 65 || event.keyCode > 90))
    return;

  var letter  = String.fromCharCode(event.keyCode);
  var correct = hangman.askLetter(letter);

  if (correct !== undefined && !correct) {
    _addLetter(letter);
    drawHangman();
  } else {
    drawCurrentWord();
  }

  afterRound();
};
```

Once the game is over, we have to give the option to start a new game. This means that we have to create the functionality to reset the controls when the "New game" buttons is clicked.

#### Reset controls

We will create a function to initialize the controls of the game. It will be called at the beginning of the `newGame` method, and will add and remove the necessary classes to the containers of the game.

```javascript

var newGameClick = function () {
  _initializeControls();
  hangman = new Hangman();
  drawCurrentWord();
};

var _initializeControls = function () {
  document.getElementById("you-win").classList   = "hide";
  document.getElementById("game-over").classList = "hide";
  document.getElementById("hangman").classList   = "";
  document.getElementById("letters").innerHTML   = "";
};
```

This was the last feature we had to implement to finish the game. Our hangman is ready to play, let's enjoy it :)

![](https://i.imgur.com/B7lJNOq.png)

## Summary

To sum up, we have seen how we can create a game like Hangman splitting it up into different parts. The best way to face this kind of problems is to create first of all the game logic, then create the layout with HTML and CSS, and finally code the necessary JavaScript that will join both the logic and the layout.

Once we have the basic functionality of this game, we could think about future actions over it. For example, we could allow to select words with spaces, add sounds to make it fancy, or call an external API to load a bigger dictionary of words.

## Extra Resources

- [Hangman online](http://www.hangman.no/)
