<picture>
  <img src="images/artemis.jpg" alt="Artemis I at LC-39B" width="100%">
</picture>

# Your Code Could Fly in Space

If you grew up on the Space Coast of Florida, you don't just watch shuttle launches. You felt them. The rumble in your chest from miles away, car alarms going off in the parking lot, and the slow realization that the tiny bright dot climbing into the sky has humans sitting on top of it. It leaves an impression.

With Artemis II bringing crew back to deep space, that feeling is coming back. And I got curious about something I never thought to ask as a kid: what software is running on those things?

Turns out, a lot of it is open source. And you can contribute to it. I've been tinkering and submitting pull requests, and I'm still not entirely sure that should be allowed.

## The Stack

NASA's open source flight software ecosystem is surprisingly deep. Here's a tour from orbit to ground, roughly in order of "how alarming is it if there's a bug."

### F' (pronounced "F Prime") - The Flight Software Framework

**[nasa/fprime](https://github.com/nasa/fprime)** | C++ | 10.7k stars

This is the big one. F Prime is JPL's framework for building flight software for spacecraft and embedded systems. It flew on the [Mars Helicopter Ingenuity](https://mars.nasa.gov/technology/helicopter/). As in, code from this repository operated autonomously on another planet.

It's component-based, designed for small spacecraft, and has genuinely excellent documentation. The codebase is approachable enough that you can start finding your way around pretty quickly.

### cFS / cFE - The Core Flight System

**[nasa/cFS](https://github.com/nasa/cFS)** | C/CMake | 1.2k stars
**[nasa/cFE](https://github.com/nasa/cFE)** | C | 475 stars

cFS is NASA's other flight software framework, and it's the older, more battle-tested sibling. Where F Prime targets smaller missions, cFS has flown on larger spacecraft and provides the Core Flight Executive (cFE): event handling, software bus, table management, time services. The stuff that keeps a spacecraft's brain organized.

If you've ever wanted to debug **buffer sizes** in code that might eventually run in orbit, this is your chance.

The whole thing is written in C. Not modern C. **Space C**. The kind where every variable name tells you exactly what it does and the comments were written by someone who assumes you understand orbital mechanics. I don't really, but that's because my knowledge in this field mostly comes from science fiction. I know enough to know what I don't know, and that keeps my tinkering mostly away from the sharp mathy bits.

### Space ROS - Robot Operating System, but for Space

**[space-ros/space-ros](https://github.com/space-ros/space-ros)** | 213 stars

ROS 2 is the de facto standard for robotics software on Earth. Space ROS adapts it for space environments: radiation tolerance, deterministic execution, the things you need when your robot is 200 million miles from the nearest reboot button.

If you have ROS experience, this is a natural entry point. If you don't, the build system alone will teach you things.

### Open MCT - Mission Control in Your Browser

**[nasa/openmct](https://github.com/nasa/openmct)** | JavaScript | 12.8k stars

This is NASA's most-starred repo, and for good reason. Open MCT is a web-based mission control dashboard framework built by JPL. It's what you'd use to visualize telemetry data, build operational displays, and generally feel like you're in a movie.

It's JavaScript! The same language we use to argue about whether tabs or spaces matter is also being used to monitor deep-space missions. Open MCT has been adopted well beyond NASA for general-purpose data visualization, which means your skills building React dashboards are more transferable to space than you'd think.

If you've built dashboards in React, you'll feel surprisingly at home here.

### The Ground System - Talking to Spacecraft from Python

**[nasa/cFS-GroundSystem](https://github.com/nasa/cFS-GroundSystem)** | Python | 91 stars

This is the ground-side companion to cFS. It's a Python GUI for commanding and receiving telemetry from a running flight software instance. If cFS is the brain on the spacecraft, this is the keyboard and monitor on the ground.

It's a great way to get hands-on with the cFS ecosystem without worrying about the orbital mechanics side of things.

### The Connector - Mission Control Web Services

**[NASA-AMMOS/openmct-mcws](https://github.com/NASA-AMMOS/openmct-mcws)** | JavaScript | 11 stars

This tiny repo connects Open MCT to JPL's actual Mission Control Web Services backend. It's the adapter between the pretty dashboard and the real telemetry systems used for deep-space operations. Only 11 stars, but those 11 people know exactly what they're doing.

Small repo, big implications. Worth poking around if you're curious about how mission control actually talks to spacecraft.

## What You'd Actually Learn

Diving into NASA's codebases teaches you things that commercial software won't:

- **C and C++ that prioritize correctness over convenience.** No frameworks, no package managers, no "just npm install it." Every dependency is deliberate. If you are a rust fan you'll love it.
- **Testing that assumes failure is fatal.** Not "the page might show an error." Fatal as in "a spacecraft might tumble." Redundancy and code hardening are prioritized accordingly.
- **Documentation written for people who will maintain this code for decades.** Not the kind of docs we write for quarterly sprints. Not too verbose either, which is nice.
- **Build systems that target hardware you can't buy on Amazon.** Cross-compilation for radiation-hardened processors is a different (fascinating) world.

And if you're a web developer, Open MCT proves that JavaScript skills translate to places you'd never expect.

## How to Get Involved

NASA's **[Open Source Catalog](https://code.nasa.gov/)** is the starting point. Filter by language, mission, or center. Most repos have open issues labeled for community contributions.

Fork something that looks interesting, try to build it, and fix whatever broke or looked wrong along the way. Turns out NASA codebases have the same kinds of bugs as everyone else's. Edge cases, off-by-ones, and infinite loops. The stakes are just a bit higher, and you may need to worry about the occasional bit-flips. But hey, at least it's not coding in Solidity.

If you're looking for a place to start, F Prime has the best onboarding experience. The tutorials walk you through building a complete flight software application, and the community is responsive to PRs.


---

[NASA Open Source Catalog](https://code.nasa.gov/) ・ [F Prime](https://github.com/nasa/fprime) ・ [Open MCT](https://github.com/nasa/openmct) ・ [cFS](https://github.com/nasa/cFS) ・ [Space ROS](https://github.com/space-ros/space-ros) ・ [Back to Profile](https://github.com/heathdutton)
