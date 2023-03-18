---
title: "Creating an embedded rust project for rp2040 - Part 1"
date: 2023-02-26T12:33:23+01:00
draft: true
---

Currently, I'm playing around a lot with Rust in general and also in the embedded ecosystem, including the
[mqttpal]({{<ref "blog/announcing_mqttpal.md" >}}) project but also single controller implementations. 

This tutorial is making use of the [raspberry pi pico](https://www.raspberrypi.com/products/raspberry-pi-pico/), being powered by an [rp2040](https://www.raspberrypi.com/products/rp2040/) chip.

First of all, let's install some prerequisites (this tutorial currently assumes Mac)

```bash
# Install Rust
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

# Add cargo bin permanently to your bash environment
echo ‘. $HOME/.cargo/bin’ >> ~/.zshenv
source ~/.zshenv

# Verify rust toolchain
rustc --version

# install cargo binutils - proxy for llvm binutils
cargo install cargo-binutils

# install llvm-tools-preview
rustup component add llvm-tools-preview

# install elf2uf2-rs - needed to build uf2 packages for pico
cargo install elf2uf2-rs
```

Then, create a minimal cargo project.

```bash
cargo init pico
```


