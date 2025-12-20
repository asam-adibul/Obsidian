# Codewalk: Generating arbitrary text: a Markov chain algorithm

@import "/doc/codewalk/codewalk.css";

 ![Pop Out Code](/doc/codewalk/popout.png "View code in new window")doc/codewalk/markov.go

code on [left](#) • [right](#) code width 70% filepaths [shown](#) • [hidden](#)

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=6&hi=44#mark)

Introduction

This codewalk describes a program that generates random text using a Markov chain algorithm. The package comment describes the algorithm and the operation of the program. Please read it before continuing.

doc/codewalk/markov.go:6,44

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=77&hi=77#mark)

Modeling Markov chains

A chain consists of a prefix and a suffix. Each prefix is a set number of words, while a suffix is a single word. A prefix can have an arbitrary number of suffixes. To model this data, we use a `map[string][]string`. Each map key is a prefix (a `string`) and its values are lists of suffixes (a slice of strings, `[]string`).  
  
Here is the example table from the package comment as modeled by this data structure:

map\[string\]\[\]string{
	" ":          {"I"},
	" I":         {"am"},
	"I am":       {"a", "not"},
	"a free":     {"man!"},
	"am a":       {"free"},
	"am not":     {"a"},
	"a number!":  {"I"},
	"number! I":  {"am"},
	"not a":      {"number!"},
}

While each prefix consists of multiple words, we store prefixes in the map as a single `string`. It would seem more natural to store the prefix as a `[]string`, but we can't do this with a map because the key type of a map must implement equality (and slices do not).  
  
Therefore, in most of our code we will model prefixes as a `[]string` and join the strings together with a space to generate the map key:

Prefix               Map key

\[\]string{"", ""}     " "
\[\]string{"", "I"}    " I"
\[\]string{"I", "am"}  "I am"

doc/codewalk/markov.go:77

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=76&hi=79#mark)

The Chain struct

The complete state of the chain table consists of the table itself and the word length of the prefixes. The `Chain` struct stores this data.

doc/codewalk/markov.go:76,79

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=82&hi=84#mark)

The NewChain constructor function

The `Chain` struct has two unexported fields (those that do not begin with an upper case character), and so we write a `NewChain` constructor function that initializes the `chain` map with `make` and sets the `prefixLen` field.  
  
This is constructor function is not strictly necessary as this entire program is within a single package (`main`) and therefore there is little practical difference between exported and unexported fields. We could just as easily write out the contents of this function when we want to construct a new Chain. But using these unexported fields is good practice; it clearly denotes that only methods of Chain and its constructor function should access those fields. Also, structuring `Chain` like this means we could easily move it into its own package at some later date.

doc/codewalk/markov.go:82,84

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=60&hi=60#mark)

The Prefix type

Since we'll be working with prefixes often, we define a `Prefix` type with the concrete type `[]string`. Defining a named type clearly allows us to be explicit when we are working with a prefix instead of just a `[]string`. Also, in Go we can define methods on any named type (not just structs), so we can add methods that operate on `Prefix` if we need to.

doc/codewalk/markov.go:60

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=63&hi=65#mark)

The String method

The first method we define on `Prefix` is `String`. It returns a `string` representation of a `Prefix` by joining the slice elements together with spaces. We will use this method to generate keys when working with the chain map.

doc/codewalk/markov.go:63,65

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=88&hi=100#mark)

Building the chain

The `Build` method reads text from an `io.Reader` and parses it into prefixes and suffixes that are stored in the `Chain`.  
  
The `[io.Reader](/pkg/io/#Reader)` is an interface type that is widely used by the standard library and other Go code. Our code uses the `[fmt.Fscan](/pkg/fmt/#Fscan)` function, which reads space-separated values from an `io.Reader`.  
  
The `Build` method returns once the `Reader`'s `Read` method returns `io.EOF` (end of file) or some other read error occurs.

doc/codewalk/markov.go:88,100

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=89&hi=89#mark)

Buffering the input

This function does many small reads, which can be inefficient for some `Readers`. For efficiency we wrap the provided `io.Reader` with `[bufio.NewReader](/pkg/bufio/)` to create a new `io.Reader` that provides buffering.

doc/codewalk/markov.go:89

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=90&hi=90#mark)

The Prefix variable

At the top of the function we make a `Prefix` slice `p` using the `Chain`'s `prefixLen` field as its length. We'll use this variable to hold the current prefix and mutate it with each new word we encounter.

doc/codewalk/markov.go:90

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=92&hi=95#mark)

Scanning words

In our loop we read words from the `Reader` into a `string` variable `s` using `fmt.Fscan`. Since `Fscan` uses space to separate each input value, each call will yield just one word (including punctuation), which is exactly what we need.  
  
`Fscan` returns an error if it encounters a read error (`io.EOF`, for example) or if it can't scan the requested value (in our case, a single string). In either case we just want to stop scanning, so we `break` out of the loop.

doc/codewalk/markov.go:92,95

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=96&hi=97#mark)

Adding a prefix and suffix to the chain

The word stored in `s` is a new suffix. We add the new prefix/suffix combination to the `chain` map by computing the map key with `p.String` and appending the suffix to the slice stored under that key.  
  
The built-in `append` function appends elements to a slice and allocates new storage when necessary. When the provided slice is `nil`, `append` allocates a new slice. This behavior conveniently ties in with the semantics of our map: retrieving an unset key returns the zero value of the value type and the zero value of `[]string` is `nil`. When our program encounters a new prefix (yielding a `nil` value in the map) `append` will allocate a new slice.  
  
For more information about the `append` function and slices in general see the [Slices: usage and internals](/doc/articles/slices_usage_and_internals.html) article.

doc/codewalk/markov.go:96,97

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=98&hi=98#mark)

Pushing the suffix onto the prefix

Before reading the next word our algorithm requires us to drop the first word from the prefix and push the current suffix onto the prefix.  
  
When in this state

p == Prefix{"I", "am"}
s == "not" 

the new value for `p` would be

p == Prefix{"am", "not"}

This operation is also required during text generation so we put the code to perform this mutation of the slice inside a method on `Prefix` named `Shift`.

doc/codewalk/markov.go:98

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=68&hi=71#mark)

The Shift method

The `Shift` method uses the built-in `copy` function to copy the last len(p)-1 elements of `p` to the start of the slice, effectively moving the elements one index to the left (if you consider zero as the leftmost index).

p := Prefix{"I", "am"}
copy(p, p\[1:\])
// p == Prefix{"am", "am"}

We then assign the provided `word` to the last index of the slice:

// suffix == "not"
p\[len(p)-1\] = suffix
// p == Prefix{"am", "not"}

doc/codewalk/markov.go:68,71

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=103&hi=116#mark)

Generating text

The `Generate` method is similar to `Build` except that instead of reading words from a `Reader` and storing them in a map, it reads words from the map and appends them to a slice (`words`).  
  
`Generate` uses a conditional for loop to generate up to `n` words.

doc/codewalk/markov.go:103,116

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=107&hi=110#mark)

Getting potential suffixes

At each iteration of the loop we retrieve a list of potential suffixes for the current prefix. We access the `chain` map at key `p.String()` and assign its contents to `choices`.  
  
If `len(choices)` is zero we break out of the loop as there are no potential suffixes for that prefix. This test also works if the key isn't present in the map at all: in that case, `choices` will be `nil` and the length of a `nil` slice is zero.

doc/codewalk/markov.go:107,110

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=111&hi=113#mark)

Choosing a suffix at random

To choose a suffix we use the `[rand.Intn](/pkg/math/rand/#Intn)` function. It returns a random integer up to (but not including) the provided value. Passing in `len(choices)` gives us a random index into the full length of the list.  
  
We use that index to pick our new suffix, assign it to `next` and append it to the `words` slice.  
  
Next, we `Shift` the new suffix onto the prefix just as we did in the `Build` method.

doc/codewalk/markov.go:111,113

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=115&hi=115#mark)

Returning the generated text

Before returning the generated text as a string, we use the `strings.Join` function to join the elements of the `words` slice together, separated by spaces.

doc/codewalk/markov.go:115

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=119&hi=121#mark)

Command-line flags

To make it easy to tweak the prefix and generated text lengths we use the `[flag](/pkg/flag/)` package to parse command-line flags.  
  
These calls to `flag.Int` register new flags with the `flag` package. The arguments to `Int` are the flag name, its default value, and a description. The `Int` function returns a pointer to an integer that will contain the user-supplied value (or the default value if the flag was omitted on the command-line).

doc/codewalk/markov.go:119,121

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=123&hi=124#mark)

Program set up

The `main` function begins by parsing the command-line flags with `flag.Parse` and seeding the `rand` package's random number generator with the current time.  
  
If the command-line flags provided by the user are invalid the `flag.Parse` function will print an informative usage message and terminate the program.

doc/codewalk/markov.go:123,124

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=126&hi=127#mark)

Creating and building a new Chain

To create the new `Chain` we call `NewChain` with the value of the `prefix` flag.  
  
To build the chain we call `Build` with `os.Stdin` (which implements `io.Reader`) so that it will read its input from standard input.

doc/codewalk/markov.go:126,127

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=128&hi=129#mark)

Generating and printing text

Finally, to generate text we call `Generate` with the value of the `words` flag and assigning the result to the variable `text`.  
  
Then we call `fmt.Println` to write the text to standard output, followed by a carriage return.

doc/codewalk/markov.go:128,129

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=0&hi=0#mark)

Using this program

To use this program, first build it with the [go](/cmd/go/) command:

$ go build markov.go

And then execute it while piping in some input text:

$ echo "a man a plan a canal panama" \\
	| ./markov -prefix=1
a plan a man a plan a canal panama

Here's a transcript of generating some text using the Go distribution's README file as source material:

$ ./markov -words=10 < $GOROOT/README
This is the source code repository for the Go source
$ ./markov -prefix=1 -words=10 < $GOROOT/README
This is the go directory (the one containing this README).
$ ./markov -prefix=1 -words=10 < $GOROOT/README
This is the variable if you have just untarred a

doc/codewalk/markov.go

[](/doc/codewalk/?fileprint=/doc%2fcodewalk%2fmarkov.go&lo=0&hi=0#mark)

An exercise for the reader

The `Generate` function does a lot of allocations when it builds the `words` slice. As an exercise, modify it to take an `io.Writer` to which it incrementally writes the generated text with `Fprint`. Aside from being more efficient this makes `Generate` more symmetrical to `Build`.

doc/codewalk/markov.go

[previous step](#) • [next step](#)