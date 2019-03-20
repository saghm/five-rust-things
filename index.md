# Five Super Helpful Rust Things That Nobody Told You About*

\* no refund if someone did actually tell you about them already

## Pretty-print debug

Normally when using `Debug` to print something, the `:?` format
operator is used. However, there are other operators that can be
used as well. One useful one is `:#?`, which formats using
newlines and indentation to make things more readable.

```rust
#[derive(Debug)]
struct Foo {
    x: i32,
    y: i32,
}

let foo = Foo { x: 1, y: 2 };

println!("Simple debug:\n{:?}", foo);
println!("Pretty debug:\n{:#?}", foo);
```

This prints out:

```
Simple debug: 
Foo { x: 1, y: 2 }

Pretty debug: 
Foo {
  x: 1,
  y: 2,
}
```

## `unimplemented!`

Sometimes, you might want to write code using a particular 
function without having to write the entire implementation. For
instance, you might want to stub out methods of a struct in
order to write tests for them, or you might want to leave out a
certain feature until later in development. The 
`unimplemented!` macro expands to an expression that will
compile regardless of the expected type.

```rust
enum VerySimpleList<T> {
    Empty,
    Elem(T, Box<VerySimpleList>),
}

impl<T> VerySimpleList<T> {
    fn len(&self) -> usize {
        match self {
            VerySimpleList::Empty => 0,
            VerySimpleList::Elem(..) => unimplemented!(),
        }
    }
}
```

## ".." struct literal operator

Sometimes, you want to create a copy of a struct with one or
more fields different. While you can do this by manually cloning
and mutating the result, there's an easier way! Using the `..`
operator in a struct literal followed by an instance of the
struct will initialize the remaining fields to those of the
instance. Conveniently, this doesn't rely on `Clone` being
implemented on the struct.

```rust
#[derive(Debug, Default)]

struct Foo {
    x: i32,
    y: i32,
}

let a = Foo { x: 1, y: 2 };
let b = Foo { x: 2, ..a };
let c = Foo { x: 2, ..Default::default() };
```

## Pattern match guards

Sometimes when pattern magic, the cases you want to handle
don't map exactly to the patterns of the data you're matching
on. For instance, you might write some code like this::

```rust
fn divide_opt(x: Option<i32>, y: Option<i32>) -> Option<i32> {
    match (x, y) {
        (Some(i), Some(0)) => None
        (Some(i), Some(j)) => Some(i / j)
	    _ => None,
    }
}
```

Alternately, you can combine two of the cases like this:

```rust
fn divide_opt(x: Option<i32>, y: Option<i32>) -> Option<i32> {
    match (x, y) {
        (Some(i), Some(j)) => {
            if j == 0 {
                None
            } else {
                Some(i / j)
            }
        }
	    _ => None,
    }
}
```

However, there's a better way than either of these! Patterns can
have a trailing conditional expression condition called a
"guard":


```rust
fn divide_opt(x: Option<i32>, y: Option<i32>) -> Option<i32> {
    match (x, y) {
        (Some(i), Some(j)) if j != 0 => Some(i / j),
	    _ => None,
    }
}
```

## Padding format operator

Want to left-pad in Rust? No need for an external package!
Simply use the `:<` format operator followed by the length of
the padding, and you'll get it!

```rust
let score1 = 100;
let score2 = 1000;
let score3 = 10000;

println!("{:>5}", score1);
println!("{:>5}", score2);
println!("{:>5}", score3);
```

This prints out:

```
  100
 1000
10000
```


Oh, did you want to pad things on the right too? No worries;
just use `:<`!

```rust
let player1 = "first player:";
let player2 = "second player:";
let player3 = "third player:";

println!("{:<14} padded!", player1); 
println!("{:<14} padded!", player2); 
println!("{:<14} padded!", player3); 
```

This prints out:

```
first player:  padded!
second player: padded!
third player:  padded!
```

But wait, there's more! If you want to pad on both sides, we've got
that too; `:^` will do the trick if you tell it how wide the 
padded string should be!

```rust
let padded = "padded";
println!("[{:^10}]", padded)
```

This prints out:

```
[  padded  ]
```

Did someone ask if you can pad with different characters? Sure
thing! Stick the character you want to pad with in front of the
arrow, and you're all set!

```rust
let title = "SCORES";

let player1 = "first player:";
let player2 = "second player:";
let player3 = "third player:";

let score1 = 100;
let score2 = 1000;
let score3 = 10000;

println!("{:_^20}", title);
println!("{:<14} {:>5}", player1, score1);
println!("{:<14} {:>5}", player2, score2);
println!("{:<14} {:>5}", player3, score3);
```

This prints out:

```
_______SCORES_______
first player:    100
second player:  1000
third player:  10000
```
