---
title: "A Simple user input collection, validation, and conversion library in Rust"
date: "2021-05-13T15:53:25.509Z"
slug: "A-Simple-user-input-collection-validation-and-conversion-library-Rust"
image: "https://images.unsplash.com/photo-1589757050226-86d0b6f2e90f?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=334&q=80"
---

# A Simple user input collection, validation, and conversion library in Rust

Writing a CLI program often requires reading and parsing user input. Today, we will create a [Rust](https://www.rust-lang.org/) library that allows us to read and convert String input from STDIN into a number of primitive types such as i32, u32, usize, etc.

This article is suitable for new and prospective Rust programmers.

## Setting Up

We begin, as most projects do, with [Cargo](https://doc.rust-lang.org/cargo/) - Rust's package manager. 

In the command line, Initialize a new project directory named `get_input` as shown below:

```bash
cargo new --lib get_input
```

The `--lib` flag tells Cargo that we're creating a new library, instead of a binary. If all went well, you should see output similar to the below:

```bash
C:\Users\rusty>cargo new --lib get_input
     Created library `get_input` package
```

**Note: If there's an existing folder of the same name in the current directory, change the project name to something else, like `get_input_tutorial`**

## Library Design

Before we write our first line of code, we need to determine, on a high level, how our code will function. What are the requirements and goals of this small library? 

Overall, we want our code to:

- Collect user input from STDIN,
- Validate that input,
- Optionally, convert that input to another type, and finally
- Perform some logic with the collected input.

## The Crate Root

When creating libraries, Cargo creates a special file called the **crate root** in the project's **`src`** directory. Packages in Rust are called crates, and one of the most important files in a library crate is the root, named `lib.rs`

We'll be working with the crate root today, located at `[project_path]\get_input\src\lib.rs`

In the [lib.rs](http://lib.rs) file, Cargo automatically generates a sample test module. This is one of Rust's several nudges towards writing robust, well-tested software. Ultimately, we should all aim to write well-tested code. 

So anyway, go ahead and **CTRL+A,  DEL** the tests till you are left with a blank page.

## Bringing Libraries Into Scope

With the `use` keyword, we'll bring the required libraries into scope. For now, we only need a module from Rust's standard library: the **[io](https://doc.rust-lang.org/std/io/index.html)** module. This library is one that provides Input/Output functionality, which we'll be using to read user input from STDIN.

```rust
use std::io;
```

`src\lib.rs`

## Function Definition

Let's go ahead and create the function definition. We'll call it **get_input** as well.

```rust
use std::io;

**pub fn get_input() {}**
```

`src\lib.rs`

`[pub](https://doc.rust-lang.org/std/keyword.pub.html)` stands for 'public' because we are allowing others to use our **get_input** function. Rust's functions and other objects are usually private by default, which means that we have to explicitly mark them as public before outside code can call into them.

For now, our function doesn't take any input parameters, neither does it return any output.

## Reading Input from STDIN

Remember that we want our function to read user input from STDIN. This is where the previously imported IO module comes into play.

```rust
use std::io;

pub fn get_input() {
    **let mut input = String::new();

    // Reads the input from STDIN and places it in the String named input.
		println!("Enter a value:");
    io::stdin().read_line(&mut input)
        .expect("Failed to read input.");
    
    print!("'{}'", input);**
}
```

In the above, we declare a new String variable named **input** and use the **Stdin** struct's **read_line** method to place the collected input into it. The **expect** method allows us to display a message on encountering an error, before crashing the program. This is called [panicking](https://doc.rust-lang.org/std/macro.panic.html) in Rust. On the last line, we print the collected input for illustrative purposes.

To call our library, create a `main.rs` file (which is a binary crate root as opposed to `lib.rs'` library crate root) in the `src` directory, right alongside `lib.rs` like so:

```rust
use get_input::get_input;

fn main() {
    get_input();
}
```

`src\main.rs`

In the above, we import the **get_input** function from our `get_input` library crate and call it from `main.rs'` main function. To run the program, execute `cargo run` in the `get_input` project directory. 

Enter 23 into the terminal, and the result should be similar to the below:

![A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled.png](A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled.png)

Notice that the closing quote is on a new line. This is because of the newline character (`\n`) appended when we read from STDIN. We'll need to remove this before we can convert our input to other types.

The **trim** method from the standard library trims leading and trailing whitespace, and this includes the newline character:

```rust
use std::io;

pub fn get_input() {
    let mut input = String::new();

    // Reads the input from STDIN and places it in the String named input.
    println!("Enter a value:");
    io::stdin().read_line(&mut input)
        .expect("Failed to read input.");
    
    **let input = input.trim();**
    
    print!("'{}'", input);
}
```

`src\lib.rs`

Running the above should produce the following:

![A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%201.png](A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%201.png)

Much better.

## Converting Strings to Other types

Rust's [parse](https://doc.rust-lang.org/std/primitive.str.html#method.parse) is a useful method that parses a string slice into another type. Using this, we can trivially convert our collected input into an integer, for example.

```rust
use std::io;

pub fn get_input() {
    let mut input = String::new();

    // Reads the input from STDIN and places it in the String named input.
    println!("Enter a value:");
    io::stdin().read_line(&mut input)
        .expect("Failed to read input.");
    
    // Convert to an i32.
    **let input: i32 = input.trim().parse().unwrap();**
    
    print!("'{}'", input);
}
```

`src\lib.rs`

In the above, we use type annotation to specify that we want the **parse** method to convert our input to an i32. We use the **unwrap** method because **parse** returns a [Result](https://doc.rust-lang.org/std/result/enum.Result.html) type. This is a compound type that represents either success or failure of an operation. You can read more about Result by clicking that link. **unwrap** allows us to access the underlying value if the operation was successful. If it wasn't, it panics.

Run the program again and input 23 once again:

![A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%202.png](A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%202.png)

The usefulness of parsing might not be immediately apparent, but this conversion means that we can now perform integer maths with it:

```rust
// Same as above

print!("'{}'", input * 3);
```

`src\lib.rs`

In the above, we multiply the input-String-turned-integer by three.

![A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%203.png](A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%203.png)

Nice.

## Error handling using match

We can convert to integers and multiply them, excellent. But what happens when we enter input that isn't easily converted to an integer?

We get this:

![A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%204.png](A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%204.png)

In the above, the highlighted part of the error message clues us into its cause: **parse** failed to convert the input "dev.to" into an integer, and it proceeded to panic because we used **unwrap**.

There are a number of ways to better handle this error. One method is using `match` and `loop`. This provides pattern-matching control flow while making sure the user enters valid input before exiting the loop. Let's add a loop-match implementation to **get_input**:

```rust
use std::io;

pub fn get_input() {

    **loop {**
        let mut input = String::new();

        // Reads the input from STDIN and places it in the String named input.
        println!("Enter a value:");
        io::stdin().read_line(&mut input)
            .expect("Failed to read input.");
        
        // Convert to an i32.
        // If successful, bind to a new variable named input.
        // If failed, restart the loop.
        **let input: i32 = match input.trim().parse::<i32>() {
            Ok(parsed_input) => parsed_input,
            Err(_) => continue,
        };**
        
        print!("'{}'", input * 3);
        **break;**
    **}**
}
```

`src\lib.rs`

In the above, we place the contents of the function body into Rust's infinite loop implementation: `loop`. This ensures the loop doesn't end unless we use the corresponding `break` keyword.

Back to the input trimming and parsing code block, there are a few things going on here. We use the `let` keyword to [shadow](https://en.wikipedia.org/wiki/Variable_shadowing) the previously-declared String variable **input** with a new one of our desired type: i32.

We then use `match` to pattern-match the output of the trimming and parsing. Remember that **parse** returns a Result type? We can then use `match` to address Result's possible variants, which are two in number. The **Ok(value)** variant indicates success, and it contains the successfully parsed value. **Err(error)** on the other hand contains the information on the error encountered.

Match's arms can be read from left to right. On the left side are the possible scenarios/cases, called **match arms**, and on the right side are the conditional code blocks to be run. 

In summary, we've told the match expression to evaluate the return value of trimming and parsing the String input, then bind the trimmed & parsed output to the variable **input** if successful. If the operation is not successful, i.e. if an error occurs, we've specified, via the `continue` keyword, that the function should continue to the next iteration of the infinite loop: forever looping until the user enters valid input.

*(I have never heard of Ctrl + C. What's that?)*

## Generalizing the function

Now, we have a function that doesn't get bogged down by trivial things such as incorrect input. What next?

Although converting Strings to integers is a useful feature for our library, we still need **get_input** to convert our String input to other types. Suppose we also wanted to convert to a u32. How would we do that? We could do the following:

```rust
use std::io;

pub fn get_input_u32() {

    loop {
        let mut input = String::new();

        // Reads the input from STDIN and places it in the String named input.
        println!("Enter a value:");
        io::stdin().read_line(&mut input)
            .expect("Failed to read input.");
        
        // Convert to a u32.
        // If successful, bind to a new variable named input.
        // If failed, restart the loop.
        let input: u32 = match input.trim().parse::<u32>() {
            Ok(parsed_input) => parsed_input,
            Err(_) => continue,
        };
        
        print!("'{}'", input * 3);
        break;
    }
}
```

`src\lib.rs`

However, one needn't be told that this would soon become quite tedious. A better solution would be to utilize Rust's useful **generics**. This would allow us to convert into many types without duplicating code. 

Let's modify **get_input** to take advantage of generics. We'll also modify the function so it now returns output:

```rust
use std::io;

pub fn get_input**<U: std::str::FromStr>**() -> **U** {

    loop {
        let mut input = String::new();

        // Reads the input from STDIN and places it in the String named input.
        println!("Enter a value:");
        io::stdin().read_line(&mut input)
            .expect("Failed to read input.");
        
        // Convert to another type.
        // If successful, bind to a new variable named input.
        // If failed, restart the loop.
        let input = match input.trim().parse::<**U**>() {
            Ok(parsed_input) => parsed_input,
            Err(_) => continue,
        };
        return input;
    }
}
```

`src\lib.rs`

In the above, we introduce generics in our function. This allows us to convert to any type that implements the **FromStr** trait bound. Trait bounds allow us specify exactly what types of values can be substituted by the generic **U**. 

The **FromStr** Trait, in particular, is implemented by types that can be constructed from a String. This, thankfully, includes the primitive types. 

With trait bounds, we're saying: "you can only substitute **U** for any type that implements **FromStr**".

Notice that we've altered the return type of the function as well. Now, we specify that **get_input** should only parse and return, by way of the generic **U**, any type that implements **FromStr** as previously discussed.

Over in `main.rs`, we modify our caller to take advantage of the flexibility provided by generics:

```rust
use get_input::get_input;

fn main() {
    let input: i32 = get_input();
    print!("'{}'", input * 3);
}
```

`src\main.rs`

We use type annotation to specify that we want an i32 returned. This means that **get_input** will substitute i32 for the previously-mentioned generic **U** in this case. We can also just as easily ask for a u32 or an isize simply by changing the type annotation.

# Extra Functionality

## Custom Prompts

Now that we've implemented our small library's core functionality, we're ready to move on to some extras. Now, we want **get_input** to display custom prompts when asking for user input, instead of the default "Enter a value:". We can achieve this by modifying the function definition to accept parameters:

```rust
use std::io;

pub fn get_input<U: std::str::FromStr>(**prompt: &str**) -> U {

    loop {
        let mut input = String::new();

        // Reads the input from STDIN and places it in the String named input.
        **println!("{}", prompt)**;
        io::stdin().read_line(&mut input)
            .expect("Failed to read input.");
        
        // Convert to another type.
        // If successful, bind to a new variable named input.
        // If failed, restart the loop.
        let input = match input.trim().parse::<U>() {
            Ok(parsed_input) => parsed_input,
            Err(_) => continue,
        };
        return input;
    }
}
```

`src\lib.rs`

In the above, we've made it so that **get_input** accepts input in form of a string slice, and displays it to the user before asking for input.

## Having STDIN on the Same Line as STDOUT

Using functionality provided by the **Write** trait, we can display the input and output streams on the same line like in the image below:

![A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%205.png](A%20Simple%20user%20input%20collection,%20validation,%20and%20co%200eacdb539168485ebfb95bfe004e9d5b/Untitled%205.png)

We can achieve that by flushing STDOUT[[1]](https://stackoverflow.com/questions/54262976/how-do-i-print-stdout-and-get-stdin-on-the-same-line-in-rust):

```rust
**use std::io::{self, Write};**

pub fn get_input<U: std::str::FromStr>(prompt: &str) -> U {

    loop {
        let mut input = String::new();

        // Reads the input from STDIN and places it in the String named input.
        **print!**("{}", prompt);
        **let _ = io::stdout().flush().expect("Failed to flush stdout.");**

        io::stdin().read_line(&mut input)
            .expect("Failed to read input.");
        
        // Convert to another type.
        // If successful, bind to a new variable named input.
        // If failed, restart the loop.
        let input = match input.trim().parse::<U>() {
            Ok(parsed_input) => parsed_input,
            Err(_) => continue,
        };
        return input;
    }
}
```

`src\lib.rs`

In the above, we flush STDOUT so that it and STDIN can be displayed on the same line.

# Conclusion

- There are other ways to convert string slices to other types. For example, using the **[from_str](https://doc.rust-lang.org/std/str/trait.FromStr.html#tymethod.from_str)** function is a common way of achieving this.
- The most structurally sound design decision would've been to use structs to more easily manage our code. I decided against this because this example library meets a very specific use case: the need to collect and parse input using a simple function. Typing `get_input("Prompt: ");` is preferable to typing `get_input::new().with_prompt("Prompt: ").get()` as in other more sophisticated libraries for this narrow use case.
- Well-documented input collection crates already exist. [Dialoguer](https://doc.rust-lang.org/std/str/trait.FromStr.html#tymethod.from_str) and [read_input](https://crates.io/crates/read_input) are two great libraries that come to mind.
- Rust's Book is the definitive introduction to Rust. [Here's a link](https://doc.rust-lang.org/book/).