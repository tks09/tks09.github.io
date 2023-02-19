---
title: "Announcing mqttpal"
date: 2023-02-19T12:17:06+01:00
draft: false
---

In the last years I worked consistently with MQTT in different projects, especially mosquitto. What mocks me most is that there are just few good UIs for monitoring and debugging MQTT servers. Additional to that, I don't know of any general purpose UI which can run as a backend on a server, collect statistics and timeseries on different topics and deliver a sophisticated Web UI. 

Additional to that I'm currently searching for a side project involving Rust, where I can try out different mechanisms and features of the Rust language as well as other technolgies, such as:

- Cargo workspaces
- Terminal UIs ([tui](https://github.com/fdehau/tui-rs))
- [Tauri](https://tauri.app/)
- [WASM](https://www.rust-lang.org/what/wasm) Export
- [Svelte JS](https://svelte.dev/)
- [RHAI](https://github.com/rhaiscript/rhai)/Lua/Python integration

The project is currently planned to consist of 2 (maybe 3) internal libraries, and several delivery methods:

- Internal:
  - mqttmonitor
    - Publishes/Subscribes to MQTT Broker
    - Able to do statistics on different MQTT Topics
    - Store statistics on those topics to different DBs (Influx/RedisTimeseries/...)
  - automation
    - Can do automation tasks on MQTT base such as
  - broker
    - integrated broker
- UI:
  - TerminalUI: htop/top like GUI to publish/subscribe
  - WebServer: backend server with UI to collect statistics, publish/subscribe etc.
  - Tauri: Frontend-Only client including svelte.


I thought about this for a while and decided to just start a project about it. The current rough project plan is:

- Setup [github repo](https://github.com/tsmt09/mqttpal) and cargo workspace ✅
- implement mqttmonitor minimal functionality, publish/subscribe, broker stats on `$SYS`
- TUI
- Tauri client including Svelte
- ...

I plan to do regular updates on this website once I implement some of the points mentioned above and discover anything interesting.
