# Codewalk: First-Class Functions in Go

@import "/doc/codewalk/codewalk.css";

 ![Pop Out Code](/doc/codewalk/popout.png "View code in new window")doc/codewalk/pig.go

code on [left](#) • [right](#) code width 70% filepaths [shown](#) • [hidden](#)

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=0&hi=0#mark)

Introduction

Go supports first class functions, higher-order functions, user-defined function types, function literals, closures, and multiple return values.  
  
This rich feature set supports a functional programming style in a strongly typed language.  
  
In this codewalk we will look at a simple program that simulates a dice game called [Pig](http://en.wikipedia.org/wiki/Pig_\(dice\)) and evaluates basic strategies.

doc/codewalk/pig.go

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=17&hi=21#mark)

Game overview

Pig is a two-player game played with a 6-sided die. Each turn, you may roll or stay.

-   If you roll a 1, you lose all points for your turn and play passes to your opponent. Any other roll adds its value to your turn score.
-   If you stay, your turn score is added to your total score, and play passes to your opponent.

The first person to reach 100 total points wins.  
  
The `score` type stores the scores of the current and opposing players, in addition to the points accumulated during the current turn.

doc/codewalk/pig.go:17,21

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=23&hi=24#mark)

User-defined function types

In Go, functions can be passed around just like any other value. A function's type signature describes the types of its arguments and return values.  
  
The `action` type is a function that takes a `score` and returns the resulting `score` and whether the current turn is over.  
  
If the turn is over, the `player` and `opponent` fields in the resulting `score` should be swapped, as it is now the other player's turn.

doc/codewalk/pig.go:23,24

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=26&hi=41#mark)

Multiple return values

Go functions can return multiple values.  
  
The functions `roll` and `stay` each return a pair of values. They also match the `action` type signature. These `action` functions define the rules of Pig.

doc/codewalk/pig.go:26,41

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=43&hi=44#mark)

Higher-order functions

A function can use other functions as arguments and return values.  
  
A `strategy` is a function that takes a `score` as input and returns an `action` to perform.  
(Remember, an `action` is itself a function.)

doc/codewalk/pig.go:43,44

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=48&hi=53#mark)

Function literals and closures

Anonymous functions can be declared in Go, as in this example. Function literals are closures: they inherit the scope of the function in which they are declared.  
  
One basic strategy in Pig is to continue rolling until you have accumulated at least k points in a turn, and then stay. The argument `k` is enclosed by this function literal, which matches the `strategy` type signature.

doc/codewalk/pig.go:48,53

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=56&hi=70#mark)

Simulating games

We simulate a game of Pig by calling an `action` to update the `score` until one player reaches 100 points. Each `action` is selected by calling the `strategy` function associated with the current player.

doc/codewalk/pig.go:56,70

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=72&hi=89#mark)

Simulating a tournament

The `roundRobin` function simulates a tournament and tallies wins. Each strategy plays each other strategy `gamesPerSeries` times.

doc/codewalk/pig.go:72,89

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=91&hi=94#mark)

Variadic function declarations

Variadic functions like `ratioString` take a variable number of arguments. These arguments are available as a slice inside the function.

doc/codewalk/pig.go:91,94

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fpig.go&lo=110&hi=121#mark)

Simulation results

The `main` function defines 100 basic strategies, simulates a round robin tournament, and then prints the win/loss record of each strategy.  
  
Among these strategies, staying at 25 is best, but the [optimal strategy for Pig](http://www.google.com/search?q=optimal+play+pig) is much more complex.

doc/codewalk/pig.go:110,121

[previous step](#) • [next step](#)