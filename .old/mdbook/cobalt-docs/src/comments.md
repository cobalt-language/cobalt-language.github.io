# Comments

Cobalt uses `#` for comments. Multiline comments start with `#` and at least one `=`. They end with at least the same number of `=`s. This is done greedily on `=`s in the opening. Multiline comments do not currently nest. The following example, adapted from the "Hello, World!" example in the introduction, demonstrates this functionality:

```
#=
 This is a multiline comment.
=#

#==
 This is another multiline comment.
==#

#===
 You can close a comment with the same or more `=`s than you 
 started with, but not less. So these won't close the comment:
 =#
 ==#
 But this will:
=======#

#==
 Comments can't be nested. There is no problem with writing 
 #========= yo! =#
 even though it is an invalid multiline comment, since the 
 parser does not treat it as a comment (since it is contained
 within a comment).
==#

#===#
 The parser is greedy on `=`s. So in the line above, the parser
 takes `#===` as the comment start, and `#` as the next character.

 It does not, for example see it as a comment start (`#=`)
 followed by a comment end (`==#`).
===#


@C(extern) fn puts(str: u8 const*);
fn main(): i32 = {
	#=
	 Multiline comments can also be put inside functions. 
	=#

	puts("Hello, World!"c); # this prints to `stdout`

	#==
	 Here's another one. 
	==#

	0 # this is the return value

	#=
	 And another. 
	=#
};

#== This is the last one, I promise! ==#
```
