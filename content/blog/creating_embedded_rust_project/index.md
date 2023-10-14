---
title: "Babysteps with Embedded Rust and RPi Pico"
date: 2023-03-19T12:33:23+01:00
draft: false
taxonomies:
  tags: ["dev", "rust", "embedded"]
---

At the moment, I'm working both with Raspberry Pi Picos in Embedded projects and Rust in System Software Projects. Today, I would like to test out the embedded capabilities of the Rust programming language and deploy some Rust embedded `#![no_std]` code to a [Raspberry Pi Pico](https://www.raspberrypi.com/documentation/microcontrollers/raspberry-pi-pico.html).

One motivation I have for this tutorial is, that there are several good tutorials on embedded Rust, but all of them provide some kind of predefined repository. While I don't want to complain about this at all and we all should be really greatful by the learning material that the Rust community is giving to us, I personally like it more to build up on a mostly empty repository, to learn exactly where we are coming from. So I personally plan to do this just as some kind of minimal project with a blinking example. Once I will go for more sophisticated projects, I will go and work with predefined repositories since they have much more capabilities than this project has.

Again, I'm writing this tutorial from a Macbook with Apple Silicon, so some things might be different in Linux or Windows.

# Prerequisites

Let's assume you have the following installed:

- **Rustup**, the installer for the Rust programming language: [Rustup](https://rustup.rs)
- A compatible code editor. With Rust, I had a lot of success with [Visual Studio Code](https://code.visualstudio.com/) and it's [Rust Extension Pack](https://marketplace.visualstudio.com/items?itemName=swellaby.rust-pack) (including the very important `rust-analyzer`).
- You've done the [Chapter 1. Quick Pico Setup](https://datasheets.raspberrypi.com/pico/getting-started-with-pico.pdf) from the official Raspberry Pi Pico installation Guide.

Done that? Good! Let's roll!

## Creating the Rust Project

Rust projects are managed with a tool called `cargo`. Cargo is a package and repository manager for the Rust ecosystem and gives you a lot of help around dependency management, testing, linting (clippy), and formatting (rust-fmt). I would always highly recommend going with cargo. So first, create a new project in a directory of your choice, for me it's `~/code/pico`

```bash
cd ~/code/pico
cargo init blink-rs
```

Cargo should've created a folder now called `blink-rs`. You can open that directory now with VSCode `nvim blink-rs`.

Open the `src/main.rs` file which is always the entry point for a `binary crate`. A `crate` is a package in rust, and a binary crate is therefore a package which builds an executable. There are also library crates, which are supposed to build, yes you guessed it, libraries ;-). You will see this:

`src/main.rs`
```rust
fn main() {
    println!("Hello, world!");
}
```

`Cargo.toml`
```toml
[package]
name = "blink-rs"
version = "0.1.0"
edition = "2021"

# See more keys and their definitions at https://doc.rust-lang.org/cargo/reference/manifest.html

[dependencies]
```

If you run `cargo run` in your console now, Cargo should compile a binary and write `Hello World!` to your terminal. If this is the case, all is good.

## How to bring this to the Pico?

In our current state, we have a Rust program which makes use of rusts `libstd`, which brings **a lot** of capabilites to work together with operating systems. If we would use an Embedded System which runs Embedded Linux for example, this would already be enough because the Kernel would abstract all hardware for us:

```
  ┌───────┐   ┌──────┐    ┌──────┐   ┌────────┐
  │main.rs├──►│libstd├───►│kernel├──►│hardware│
  └───────┘   └──────┘    └──────┘   └────────┘
```

Since we want to run our software natively for the Raspberry Pi Pico, and usually there is no libstd implementation for single hardware, we need to skip the libstd by instructing rust to do `#![no_std]` in the first line of our code:

```rust
#![no_std]

fn main() {
    println!("Hello, world!");
}
```

The first thing we'll notice is that on `cargo build`, rust will immediately complain about println! not being known. Yes, that's true, since it's part of `libstd`. You might ask now what you can still use, the [Embedded Rust Book has a really good chapter on this](https://docs.rust-embedded.org/book/intro/no-std.html).

If you try to build now, you will see a couple of errors:
```bash

❯ cargo run
   Compiling blink-rs v0.1.0 (/Users/ts/code/pico/blink-rs)
error: cannot find macro `println` in this scope
 --> src/main.rs:4:5
  |
4 |     println!("Hello, world!");
  |     ^^^^^^^

error: `#[panic_handler]` function required, but not found

error: language item required, but not found: `eh_personality`
  |
  = note: this can occur when a binary crate with `#![no_std]` ...
  = help: you may be able to compile for a target that doesn't ...

error: could not compile `blink-rs` due to 3 previous errors
```

Obviously, println! is not available anymore, since we got rid of libstd. Additionaly, Rust does not know how to handle `panics` since the `#[panic_handler]` function is not defined anymore. And finally Rust is also not able to compile for a target where `eh_personality` (eh = exception handling) is not defined, which is in case for "standard environments" like linux/mac/windows automatically being done by rusts libstd.

### So, what do we need to do now to bring this code to a Raspberry Pi Pico? 
First of all, define the right target. With rust, we can install it by writing `rustup target install thumbv6m-none-eabi`.

Then, we need to tell cargo to build for this target. Instead of using `Cargo.toml`, cargo uses `.cargo/config.toml` here. So we create a `.cargo` folder and crate a file `config.toml` with the follwing contents:

```toml
[build]
target="thumbv6m-none-eabi"
```

Once we've done this, we need to get the right crates into our system. The Embedded Rust community tries to standardize abstraction layers to streamline implementation for different board, driver and bus abstractions. These are the typical abtraction crates that we need for embedded development:

#### Architecture Support Crates

Our Raspberry Pi Pico runs a RP2040 Microcontroller, which is a Cortex-M implementation. So our architecture support crate is called `cortex-m` and `cortex-m-rt` (rt for runtime).

#### Peripheral Access Crate

The PAL is based on your microcontroller. By using this crate, you can access internal periphals of the chip you are using. In our case - the Raspberry Pi Pico which runs on a RP2040, so the peripheral access crate is called `rp2040-pac`.

#### Hardware Abstraction Layer

So we're running a Cortex-M. Good. But which cortex M? For this, we need to define the _Hardware Abstraction Layer_, which in our case is the crate called `rp2040-hal`.

#### Board Support Crate:

We run a Raspberry Pi Pico, so there's a board support crate available which is called `rp-pico`. In case you build your own hardware, usually the board support crate is what you would need to write on your own (despite your layout is 100% compatible to an existing board). The board support crates usually contain the PAC and HAL.


```

  Code     │  Board    │  Hardware    │  Architecture │   Actual
           │  Support  │  Abstraction │  Support      │   Hardware
           │           │ ┌──────────┐ │ ┌───────────┐ │ ┌────────────────┐
           │           │ │rp2040-hal│ │ │cortex-m   │ │ │                │
 ┌───────┐ │ ┌───────┐ │ └──────────┘ │ └───────────┘ │ │ Microcontroller│
 │main.rs│ │ │rp-pico│ │              │               │ │                │
 └───────┘ │ └───────┘ │ ┌──────────┐ │ ┌───────────┐ │ │     rp2040     │
           │           │ │rp2040-pac│ │ │cortex-m-rt│ │ │                │
           │           │ └──────────┘ │ └───────────┘ │ └────────────────┘
           │           │  Peripheral  │               │
           │           │  Access      │               │
           │           │  Crate       │               │

```


### Bring in the dependencies

So, we want to add these dependencies into our project. For that, we edit the Cargo.toml file with the following contents:

`Cargo.toml`
```toml
[dependencies]
# for the cortex microcontroller
cortex-m = "0.7.7"
cortex-m-rt = "0.7.3"
# board support crate
rp-pico = "0.7"
# for GPIO traits
embedded-hal = "0.2.7"
# to handle panics
panic-halt = "0.2"
```

### Bring in the code

At this point, I actually planned to do write down the exact code for the embedded rust project, but then I realized that the creators of the `rp-pico` Board Support Create actually did a great job in providing a [perfectly commented blink example](https://github.com/rp-rs/rp-hal-boards/blob/main/boards/rp-pico/examples/pico_blinky.rs). **Please copy the codes content to your main.rs, since we will use it for now. Some general steps through the code**:

The `main` function is annotated with a `#[entry]` macro in line 41, to tell the controller where to start. At the start of the main function, you `take()` the Periphals and Coreperiphals of your controller to the currents scope ownership. This is an important concept in Rust, where [ownership of a value/variable is always clear](https://doc.rust-lang.org/book/ch04-01-what-is-ownership.html).

Then, you setup a watchdog and clocks, get a delay functionality from the cortex's hardware implementation and some other I/O structures. Finally, you end up in the `loop`, which in rust is equal to a `while(1)` in C or the `loop()` function in Arduino.

```rust
// Blink the LED at 1 Hz
loop {
    led_pin.set_high().unwrap();
    delay.delay_ms(500);
    led_pin.set_low().unwrap();
    delay.delay_ms(500);
}
```

In this code, we blink the LED once a second.

### Build the Code

In Rust, building code is pretty simple, you just type `cargo build` or `cargo build --release`. We build the release code. You should find a file in `target/thumbv6m-none-eabi/release/` called `blink-rs`. This is an ELF file, which you cannot just load to your RP2040 now.

There's a tool called `elf2uf2` you can install with `cargo install elf2uf2-rs`. With this, you can translate the file (after you reload the terminal):

```
elf2uf2-rs target/thumbv6m-none-eabi/release/blink-rs
```

Wait, there's an error?

```
Error: "Memory segment 0x010000->0x010094 is outside of valid address range for device"
```

Okay, it seems that the compiler did something wrong and used more memory than the pico is available to give to us. [Googling](https://github.com/rp-rs/rp-hal/issues/307#issuecomment-1058952616) tells us that we need to specify a memory.x file which is used by the linker to guarantee a correct output layout for a raspberry pi pico. So let's take a look at what is happening here:

`memory.x`
```
MEMORY {
    BOOT2 : ORIGIN = 0x10000000, LENGTH = 0x100
    FLASH : ORIGIN = 0x10000100, LENGTH = 2048K - 0x100
    RAM   : ORIGIN = 0x20000000, LENGTH = 256K
}

EXTERN(BOOT2_FIRMWARE)

SECTIONS {
    /* ### Boot loader */
    .boot2 ORIGIN(BOOT2) :
    {
        KEEP(*(.boot2));
    } > BOOT2
} INSERT BEFORE .text;
```

This is a so called _Linker Script_ for the ld linker which is part of the Gnu Compiler Collection (gcc). 
What it is basically doing is defining boot, flash and ram locations in memory and then making sure that the BOOT2 section is kept where it already is.

Comparing this and the Memory Segment error from the elf2uf2-rs tool, I'm pretty happy that we were notified about invalid adress memory, because it seems that we would've overwritten the huge parts of the existing bootloader.
Good, so we create a new file at the project root level called `memory.x` with the contents mentioned above, and change the `.cargo/config.toml` to include:

```toml
[target.thumbv6m-none-eabi]
rustflags = [
"-C", "link-arg=-Tlink.x",
]

[build]
target="thumbv6m-none-eabi"
```

Now, run `cargo clean` and `cargo build --release` followed by once more `elf2uf2-rs target/thumbv6m-none-eabi/release/blink-rs`. If you see no error, boot your Raspberry Pi Pico into bootloader mode by attaching microusb while pressing `BOOTSEL` button on the board. Now rerun the last command with a -d at the end to flash the device and see your Raspberry Pi Pico blinking! **Great job!**

You can find the repository in [my github](https://github.com/tsmt09/blink-rs).


### Where to go from here?

So we made it work to blink a LED on a Raspberry Pi Pico using Rust. That's great. From here you can basically go everywhere. Good learning resources for embedded rust you can find here:


- [Awesome Embedded Rust](https://github.com/rust-embedded/awesome-embedded-rust)
- [Embedded Discovery Book](https://rust-embedded.github.io/discovery)
- [RP Pico Crate with great examples](https://github.com/rp-rs/rp-hal-boards/tree/main/boards/rp-pico)


You can find the source for this code in 
Thank you for reading through this. If you have any questions or feedback for things I can do better, feel free to send me an email to `hi@tobischmitt.net`.

