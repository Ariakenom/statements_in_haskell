putStrLn

# Target audience
People who know something like Java and want a brief introduction how you interact with the world in haskell.

# hello world

```
main = print "Hello world"
```

The classic! Just like in C `main` defines what the program will do. In this case `main` is a single print statement. 

Function calls in haskell don't use parenthesis so `putStrLn("hello")` becomes `putStrLn "hello"`. These types of syntax differences can be a big obstacle to otherwise clear similarities. 

# A block of statements

Statements can be many different things. It can be a block of statements, which is called a do-block because it starts with the do-keyword. 

In this post I will separate statements within a do-block using ;. Usual Haskell style uses "layout" which replaces {} and ; with indentation. The code in this post is indented so that it would work without with the {} and ; as well. 

```
main = do {
    print "hello";
    print "world";
}
```

Since `print "world"` is a statement we could replace that with another do-block. 

```
main = do {
    print "hello";
    do {
        print "world";
        print "!";
    };
    print "!";
}
```

# Getting intput

Inside a do block we can get a result from a statemnt using `<-`. 

```
main = do {
    print "What is your name?";
    name <- getLine;
    print ("Hello " ++ name ++ "!");
}
```

```
main = do {
    x <- print "what is the result of print?";
    print x; -- the placeholder value ()
}
```

The placeholder value `()` is an empty tuple. It is like void in C or None in Python. The type of the value is also written `()`.

# If

To use if with our statements we write an if-expression that results in a statement. That statement can of course be a do-block.

```
main = if False then print True else print False
```

# Looping

`main` is itself a statement. We can run `main` to go back to the beginning. In C or Python this can cause an issue with the stack but that's not an issue here, even if the call isn't tail recursive.

Putting all of the parts together we can write an interactive program.

```
main = do {
    input <- getLine;
    if input == "quit"
    then 
        print "quit"
    else 
        do {
            print "looping";
            main;
        };
}   
```

# A number guesser

`read` parses a string into a number.
`let x = e` is similar to `x <- e` but `e` is an expression instead of a statement. `let x = getLine` does not read a line from the user, instead `x` just becomes a different name for the statement `getLine`.

```
secret_number = 42

main = do {
    let myGetLine = getline; 
    input_string <- getLine;
    let input_int = read input_string;
    if input_int == secret_number;
    then 
        print "You guessed right!"
    else do {
        if input_int < secret_number
        then print "Your guess was too low."
        else print "Your guess was too high.";
        main;
        };
}
```

# More

We've now built a interactive program! But there are more things to understand about how statements work in haskell.

# Purity

`head` is a function that results in the first item from a list.

```
main = head [print "pure", print "impure"] 
```

In this example only "pure" will be printed. In most C or python both pure and impure would be printed. This is a large difference compared to other languages and is called "purity" or "no side-effects". This is sometimes said to be because Haskell is "lazy" but that's wrong, laziness is unrelated to this.

As we've seen a statement can be built out of several statements using do-blocks or if-statements. In haskell every statement that has an effect are a part of `main` in this way. 

So in the code above only the first item of the list is defined to be a part of main. The second item is a part of an expression that computes what goes into main but, since it isn't the result, it is not a part of the statement `main`.

# Defining a function

We can define other statements recursively like this to create different loops. And we can define a function that results in a statement to carry state between the iterations.

`pure ()` is a placeholder statement that does nothing.

```
main = count 0

count n = do {
    print n;
    if n == 100
    then pure ()
    else count (n+1);
}
```

Just so Haskell doesn't seem too terrible I'll mention that there are shorter ways to write this using functions I haven't explained.

```
main = for [0..100] print
```

# Lambda

# Monad!?

todo pure.

The model Haskell uses to express a block of statements is called a monad. The model is made of a 2 different kind of functions. 

First it creates a new function for each statement in a do-block. Secondly it uses a combining function `>>=`, called "bind", between each statement. Bind takes two arguments `bind x y`.  `x` is a statement that produces some result. `y` is a function that takes `x`s result as an argument and produces another statement. The job of bind is to combine `x` and `y`.

```
main = do {
    input1 <- getLine;
    input2 <- getLine;
    print x;
}
```

```
main = (
    bind getLine (\input1 ->
        bind getLine (\input2 ->
            print input
        )
    )
)
```

The model is used for many different things like parsing or, in our case, to describe how a program interacts with the outside world.
