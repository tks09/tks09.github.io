---
title: "Now"
date: 2023-10-14T20:01:15+02:00
updated: 2024-07-26T21:30:00+02:00
---

## Indieweb & basic internet technologies

### Indieweb
I'm currently reading a lot about the Indieweb. Basically the indieweb is the internet how it was meant to be. Decentralized and owned by **many** people and not [a hand full of companies](https://www.makeuseof.com/who-owns-the-internet/). As the linked articles says, the internet hardware is bascially owned by very few telecommunication companies per country and it's software (and also data!) is controlled by basically just a few companies (google, facebook, microsoft).

The indieweb is a movement against that. I'm currently exploring a lot and it's just fascinating and great and brings back some early 2000s vibes for me.

### Freelancing and Rust trainings

I also try to do freelance projects using Rust and also get some gigs to do my async-Rust trainings based on my [tokio-web-demo](https://github.com/tsmt09/tokio-web-demo).

### Basic tech
Besides the Indieweb, I'm currently exploring *basic internet technologies*. What I mean with this is that in the last years I got pretty disconnected with a huge part of web development which is the frontend.

I have this [mqttpal](https://github.com/tsmt09/mqttpal) side project, which is basically meant to be a debugging and statistics webserver for "mosquitto" and other mqtt brokers. My goal is to build a very dynamic website supporting technologies such as websockets for live debugging of MQTT Topics, while not making use of any single page application framework such as React or Vue. To do that, I make use of:

- [Actix-Web](https://actix.rs/) for the backend
- [Askama](https://djc.github.io/askama/askama.html) as templating engine
- [HTMX](https://htmx.org/) and [hyperscript](https://hyperscript.org/) to implement a dynamic frontend
