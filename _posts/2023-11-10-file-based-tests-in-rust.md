# Background
Have you ever had an assignment where you were give a bunch of files, 
and had to input them to your rust program? Have you ever wished,
*if only I could generate a test for each file that I was given*. 
Well there is! It's something that you can do with the `test-each` crate!

# Getting Started
To get started, let's set up a simple function. This will just check to see if 
the contents of the string is "one", "two", or "three", but this could be 
extended to do anything with a string.

```rust
fn is_one_two_three(str: &str) -> bool {
	match str {
    	"one" => true,
    	"two" => true,
    	"three" => true,
    	_ => false,
	}
}
```

Now that we have that function, we could write unit testing in the traditional 
way, or we could make files to test for us! Let's do that, I like that idea!

```shell
 echo one >> test/one.txt
 echo two >> test/two.txt
 echo three >> test/three.txt
```

Now that we have our files, we can add the test-each crate.
```shell
cargo add test-each
```

Now lets make the tests:

```rust
#[cfg(test)]
mod file_tests {
	use crate::is_one_two_three;

	#[test_each::file(glob = "tests/*.txt", name(segments = 1))]
	fn parse_file(src_file_contents: &str) {
    	assert!(is_one_two_three(src_file_contents.trim()))
	}
}
```

# Explanation
Here we can see the 
`#[test_each::file(glob = "tests/*.txt", name(segments = 1))]`. The 
`glob = "tests/*.txt"` tells the compiler to generate a test for every file 
that matches the glob pattern. The `name(segments = 1)` tells the compiler 
what to name the functions. In this case, it will append the name of the file 
to the test function name. This is just the surface though, check the
[docs](https://docs.rs/test-each/0.2.1/test_each/) for more info on what else 
you can do.

Also notice: the test function parameters. `parse_file(src_file_contents: &str)`
this means that it will, in fact, read the contents of the file, and pass it to
the function as `src_file_contents` in this case. You could alternatively also 
have a PathBuf parameter type and it would pass you the file path of the 
testing file.

To run, its as simply as running `cargo test` like normal testing!

# Why This Crate Was Useful
This was useful to me during a project where I was writing a
[Logo](https://en.wikipedia.org/wiki/Logo_(programming_language))
interpreter; I had a test file for every feature of the language I was 
implementing. At first, I was writing tests to for every file to make sure the 
contents were properly handled, but I later found this crate to test files in 
bulk. Instead of writing a repetitive test for every file, all I did was write
three functions: 
 - one for a parse error
 - one for a runtime error
 - one for a success

This vastly improved extensibility and encouraged me to write more test files,
since the code to test the file was generated automatically.



## Full Contents of Main

```rust
#[cfg(test)]
mod file_tests {
	use crate::is_one_two_three;

	#[test_each::file(glob = "tests/*.txt", name(segments = 1))]
	fn parse_file(src_file_contents: &str) {
    	assert!(is_one_two_three(src_file_contents.trim()))
	}
}

fn is_one_two_three(str: &str) -> bool {
	match str {
    	"one" => true,
    	"two" => true,
    	"three" => true,
    	_ => false,
	}
}

fn main() {}
```

