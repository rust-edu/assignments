---
layout: work
worktitle: Bare Metal Game
tags:
- Operating Systems
- Moderate
---

## Acknowledgement

This assignment was adapted from [materials](https://os.phil-opp.com/) developed by 
[Philipp Oppermann](https://github.com/phil-opp).

## Description

Classic early video games such as [Space Invaders](https://en.wikipedia.org/wiki/Space_Invaders), 
[Asteroids](https://en.wikipedia.org/wiki/Asteroids_(video_game)), and
[Pac-man](https://en.wikipedia.org/wiki/Pac-Man) were implemented without an operating system.
The games mediated all hardware interaction by themselves.

In this project, you will use the [Pluggable Interrupt OS](https://crates.io/crates/pluggable_interrupt_os)
to develop a bare-metal video game. The graphical aspect of the game will be rendered in the VGA buffer.
You are encouraged to be creative in using VGA characters to represent your game graphics.

You are welcome to develop an original game or to create your own interpretation of a classic game.
As an example, feel free to reference [Ghost Hunter](https://github.com/gjf2a/ghost_hunter),
my own interpretation of a well-known classic.

## Interrupts

The primary purpose of this project is for you to gain experience writing software that handles
[hardware interrupts](https://os.phil-opp.com/hardware-interrupts/). When an event pertinent to the hardware
occurs, it notifies the CPU by signaling an interrupt. The CPU responds to the interrupt by suspending execution
of whatever code it is running and running the code designated for handling the interrupt. Naturally, 
interrupt-handling code should complete as quickly as possible so that the CPU can resume normal execution.

This project will be very different in flavor, because **all** of the code you write will be executed by 
interrupt handlers. You will create two interrupt handlers for this project:
* Your **keyboard interrupt handler** will update the game based on player input.
* Your **timer interrupt handler** will update the game based on time-dependent gameplay elements of your design.

## Setup

The [Pluggable Interrupt OS](https://crates.io/crates/pluggable_interrupt_os) provides a convenient framework for
specifying to the CPU the code to be executed to handle each interrupt. 

I have created a [template](https://github.com/gjf2a/pluggable_interrupt_template) 
for you to use as a starting point for your projects. To start your project, clone 
the [Pluggable Interrupt Template](https://github.com/gjf2a/pluggable_interrupt_template) 
project. In order to build the project, you'll also need to install:
* [Qemu](https://www.qemu.org/)
* Nightly Rust:
  * `rustup default nightly`
* `llvm-tools-preview`:
  * `rustup component add llvm-tools-preview`
* The [bootimage](https://github.com/rust-osdev/bootimage) tool:
  * `cargo install bootimage`
  
Once the template is up and running, you will be ready to implement your own interrupt handlers! Of course,
you'll want to change the project name and authors in 
[Cargo.toml](https://github.com/gjf2a/pluggable_interrupt_template/blob/master/Cargo.toml), and you'll also 
want to set up your own GitHub repository for it.

The template project demonstrates a simple interactive program that uses both keyboard and timer interrupts.
When the user types a viewable key, it is added to a string in the middle of the screen.
When the user types an arrow key, the string begins moving in the indicated direction.
Here is its [`main.rs`](https://github.com/gjf2a/pluggable_interrupt_template/blob/master/src/main.rs):

```
#![no_std]
#![no_main]

use lazy_static::lazy_static;
use spin::Mutex;
use pc_keyboard::{DecodedKey, KeyCode};
use pluggable_interrupt_os::HandlerTable;
use pluggable_interrupt_os::vga_buffer::clear_screen;
use pluggable_interrupt_template::LetterMover;
use crossbeam::atomic::AtomicCell;
use pluggable_interrupt_os::println;

#[no_mangle]
pub extern "C" fn _start() -> ! {
    HandlerTable::new()
        .keyboard(key)
        .timer(tick)
        .startup(startup)
        .start()
}

lazy_static! {
    static ref LETTERS: Mutex<LetterMover> = Mutex::new(LetterMover::new());
    static ref LAST_KEY: AtomicCell<Option<DecodedKey>> = AtomicCell::new(None);
}

fn tick() {
    let mut letters = LETTERS.lock();
    match LAST_KEY.swap(None) {
        None => {}
        Some(key) => letters.key(key)
    }
    letters.tick();
}

fn key(key: DecodedKey) {
    LAST_KEY.store(Some(key));
}

fn startup() {
    clear_screen();
}
```

The **_start()** function kicks everything off by placing references to our interrupt handling functions
in a **HandlerTable** object. Invoking **.start()** on the **HandlerTable**
starts execution. The PIOS sits back and loops endlessly, relying on the event handlers to
perform any events of interest or importance.

I created the [`LetterMover`](https://github.com/gjf2a/pluggable_interrupt_template/blob/master/src/lib.rs)
`struct` to represent the application state. As interrupts are inherently concurrent, we wrap the object in a 
[`Mutex`](https://doc.rust-lang.org/book/ch16-03-shared-state.html). In order to delay constructing the 
object until it is first referenced, we employ the 
[Lazy Static](https://os.phil-opp.com/vga-text-mode/#lazy-statics) macro.

This shows the basic design that all of these projects should employ:
* Create a `main.rs` that sets up the interrupt handlers.
* Write one-line handlers for the timer and keyboard that reference a shared game-state object.
* Place all of the game functionality within the game-state object, defined in **lib.rs**.

The **tick()** function calls the `LetterMover::tick()` method after unlocking the object. 
Similarly, the **key()** function calls the `LetterMover::key()` method, again after unlocking
the object.

Here is the rest of its code, found in its [`lib.rs`](https://github.com/gjf2a/pluggable_interrupt_template/blob/master/src/lib.rs) file:
```
#![cfg_attr(not(test), no_std)]

use bare_metal_modulo::{ModNumC, MNum, ModNumIterator};
use pluggable_interrupt_os::vga_buffer::{BUFFER_WIDTH, BUFFER_HEIGHT, plot, ColorCode, Color, is_drawable};
use pc_keyboard::{DecodedKey, KeyCode};
use num::traits::SaturatingAdd;

#[derive(Copy,Debug,Clone,Eq,PartialEq)]
pub struct LetterMover {
    letters: [char; BUFFER_WIDTH],
    num_letters: ModNumC<usize, BUFFER_WIDTH>,
    next_letter: ModNumC<usize, BUFFER_WIDTH>,
    col: ModNumC<usize, BUFFER_WIDTH>,
    row: ModNumC<usize, BUFFER_HEIGHT>,
    dx: ModNumC<usize, BUFFER_WIDTH>,
    dy: ModNumC<usize, BUFFER_HEIGHT>
}

impl LetterMover {
    pub fn new() -> Self {
        LetterMover {
            letters: ['A'; BUFFER_WIDTH],
            num_letters: ModNumC::new(1),
            next_letter: ModNumC::new(1),
            col: ModNumC::new(BUFFER_WIDTH / 2),
            row: ModNumC::new(BUFFER_HEIGHT / 2),
            dx: ModNumC::new(0),
            dy: ModNumC::new(0)
        }
    }

    fn letter_columns(&self) -> impl Iterator<Item=usize> {
        ModNumIterator::new(self.col)
            .take(self.num_letters.a())
            .map(|m| m.a())
    }

    pub fn tick(&mut self) {
        self.clear_current();
        self.update_location();
        self.draw_current();
    }

    fn clear_current(&self) {
        for x in self.letter_columns() {
            plot(' ', x, self.row.a(), ColorCode::new(Color::Black, Color::Black));
        }
    }

    fn update_location(&mut self) {
        self.col += self.dx;
        self.row += self.dy;
    }

    fn draw_current(&self) {
        for (i, x) in self.letter_columns().enumerate() {
            plot(self.letters[i], x, self.row.a(), ColorCode::new(Color::Cyan, Color::Black));
        }
    }

    pub fn key(&mut self, key: DecodedKey) {
        match key {
            DecodedKey::RawKey(code) => self.handle_raw(code),
            DecodedKey::Unicode(c) => self.handle_unicode(c)
        }
    }

    fn handle_raw(&mut self, key: KeyCode) {
        match key {
            KeyCode::ArrowLeft => {
                self.dx -= 1;
            }
            KeyCode::ArrowRight => {
                self.dx += 1;
            }
            KeyCode::ArrowUp => {
                self.dy -= 1;
            }
            KeyCode::ArrowDown => {
                self.dy += 1;
            }
            _ => {}
        }
    }

    fn handle_unicode(&mut self, key: char) {
        if is_drawable(key) {
            self.letters[self.next_letter.a()] = key;
            self.next_letter += 1;
            self.num_letters = self.num_letters.saturating_add(&ModNumC::new(1));
        }
    }
}
```

The keyboard handler receives each character as it is typed. Keys representable as a `char`
are added to the moving string. The arrow keys change how the string is moving.

  
## Requirements

This project is very flexible in its requirements. A successful submission will:
1. Run in the Qemu x86 virtual machine without any supporting operating system.
2. Be built upon the [Pluggable Interrupt OS](https://crates.io/crates/pluggable_interrupt_os).
3. Only use `#[no_std]` Rust features and crates.
4. Enable the player to control a graphically depicted avatar using the keyboard.
5. Include gameplay elements that advance based on clock ticks and cause the game to eventually end if the player
  does not react to them appropriately.
6. Run indefinitely without any panics.
7. Allow the user to restart the game when it ends.
8. Display the user's current score.



------------------------------------------------------------------------
