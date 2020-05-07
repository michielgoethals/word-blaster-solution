# Word Blaster

A C++ typing game.

Word Blaster challenges the player to type a list of words as fast as possible and without mistakes. It tracks the scores of the player using a scoreboard and also allows the user to select the appropriate difficulty.

The words are selected randomly from text files located on the file system.

## Building and running the game

This project is equipped with a make file to build and run the game. Just run the command bellow.

```cpp
make run
```

### Doxygen

This project contains doxygen comments which can be generated into a fancy API site. Just install [doxygen](http://www.doxygen.nl/) and run the `doxygen` or `make docs` command (you may need to add doxygen to your PATH) in the root directory of this project. The html output can be found at [./docs/html/index.html](./docs/html/index.html).

## Part of C++ Course

This project was a challenge for the students of the VIVES University College in Bruges as part of the C++ course.

## Original Assignment

Build a word typing game that challenges the user to type over words as fast as possible.

Let the user login using a nickname. Also allow selection of difficulty.

Select random words from a file. Can be plain text file. Make sure that the difficulty influences the word selection from the file.

The score should be calculated based on the correctness of the user's typing and also the speed with which the user finishes each challenge. Calculate a score based on at least these parameters.

Allow the score to be saved to a file and displayed in a scoreboard.

Try to make the game a bit more appealing by using for example ascii art or other techniques for visualization inside the console.

Document the project with a decent `README.md` and UML class diagrams. Also explain new techniques or things you used to build the app.

Also include a `Makefile` so the project can be easily built using a single `make` command.

## Steps Taken

Step titles are same as the commit messages. This way you can follow along.

### Step 1 - Simple Hello World

Output basic message and make sure everything (compiler and makefile) works.

### Step 2 - A WordList class

WordList can track a list of words and provide randomly selected word.

Single responsibility so no loading from file and such. Responsibility of another class.

### Step 3 - A WordLoader class

Words need to be loaded from a file. `WordLoader` loads words from a file and places them in a `WordList`.

Later on different files will exist for the different difficulties. However, `WordLoader` does not have to know this.

`WordLoader` does not need to keep any state for its single method. Because of this the method is made `static` which means it is not called on an object of `WordLoader` but rather on the class itself.

### Step 4 - Some Game Logic

Basic game logic consists of asking the user to repeat a certain number of words. This can be accomplished by a simple `for` construct. In this step the code is placed in main. Next steps will refactor this.

### Step 5 - A Game class

This kind of logic cannot be kept in main. This typically screams for a `Game` class with a basic `play()` method. `WordList` will also need to be moved as attribute of `Game` (composition).

Tip: Just move the `for` loop into the `play()` method and check out what extra information you need for the code to work.

This still requires some serious refactoring.

### Step 6 - User Input Output class

Problem with the current `Game` class is the fact that it handles user IO. If possible, it is a good idea to separate the user IO from the game logic. This would actually allow the game to be used with a GUI instead of a terminal and only a single class would have to be provided.

If this separation is done early in the development process, it's a much less painful operation.

Basically we need a class that has some methods that can request a word from the user, output the next word, output some stats about his/her score and so on. This class will not need to track any state as each method call will require all the needed information to be supplied. The `Game` class tracks all state here.

The end result should be a `Game` class without a single `cout`, `getline` or `cin`. The `UserIO` methods should not contain any game logic. This means that for example the comparison of the generated word and the user input should happen in the game class.

### Step 7 - A Score class

At the moment no score is tracked for the player. One could state that we could just track the number of wrongly typed words in the Game class but if the scoring mechanism will get a bit more complex, it will needs it's own class.

For the moment we can use the length of the word as a score parameter. Later we can add timing and such.

In one of the following steps we will also be adding a `Player` to the game to which an object of `Score` will belong.

Game will need to track the `Score` of the current game. An attribute can be added to the class for this.

Let's also add a method to `UserIO` to output the final score of the user.

### Step 8 - A Player class

It's time to add a player. This will be required once we want to start keeping a score board.

When the application starts we will ask the user for a nickname and store this inside of the `Player` class. The player should then be injected into the `Game` object upon construction. This will later allow us to ask the player if he/she wishes to start another game without requiring hem/her to enter the nickname again.

We will also alter the `Score` class so it keep a pointer to the `Player` to which the score belongs. This can also be accomplished via the constructor.

Why not add a Score object as an attribute to Player? Well basically because a Player will be able to have multiple score's in the `Scoreboard`. That would mean we would have to track these in a list (inside of player). On top of that the Player object doesn't really need to know all of its scores. The Scoreboard will need to track these.

The Score object of `Game` should also be moved in the `play` method as a local variable (instead of an attribute) for a couple of reasons:

* we only need it in the play method
* every time play is called a new score is required
* the constructor of game takes in a Player object, we cannot use a pointer to this object to initialize score as it is a copy which will go out of scope after the constructor is done.

If we were to follow this route (as an attribute of game) we would need to create the score on the heap.
