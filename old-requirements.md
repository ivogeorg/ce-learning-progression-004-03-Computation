_**TODO:**_
1. _Incorporate materials from [programming notebooks](https://github.com/CE-Curriculum-Development/mat-cpe-1040)._  
2. _Rearrange for the _study, apply, present_ structure (e.g. [Lesson and assignment 006](https://github.com/ivogeorg/ce-lesson-and-asst-006-flip-flops/blob/master/lesson-and-assignment.md)._  
3. No need for 5V supply. Switch to 3.3V from breadboard PS.  

---
# CPE 1040 - Spring 2020

## Assignment 4: Microbit LEDs and GPIO Pins

Author: Ivo Georgiev, PhD  
Last updated: 2020-02-18  
Code: bfa7bad28c3293419d0214510037220abb9b1146  

This is assignment 4 for the Spring 2020 installment of the CPE 1040 - Intro to Computer Engineering course at MSU Denver.

### Overview

This assignment introduces the GPIO pins of the micro:bit. _Refer to the [criteria](criteria.md) for submitted assignments!_

### Requirements

#### 1. Simple external LED circuit
1. On a workstation breadboard, build a simple DC circuit with a 5V voltage source, a 330 or 470 Ohm resistor, and an LED.
2. Measure the voltage drop V<sub>LED</sub> across the LED with a multimeter.
3. Measure the current I in the circuit with a multimeter.
4. Calculate by Ohm's Law (V = IR or R = V/I) the effective resistance R<sub>LED</sub> of the LED.
5. Write your measured and calculated values in the [measurements](#measurements) section at the bottom of this [README](README.md).
6. Record a brief video of your circuit setup and its operation and link it in the [demo video](#demo-video) section at the bottom of this [README](README.md).

#### 2. Micro:bit breakout tutorial
1. Connect your micro:bit to one of the [breakout + breadboard](https://imgur.com/gallery/qqRxBby) kits distributed in class.
2. Follow the instructions in the SparkFun [micro:bit breakout board hookup guide](https://learn.sparkfun.com/tutorials/microbit-breakout-board-hookup-guide). 
   1. Build the circuit, using the 3 resistors and 3 LEDs of different colors, distributed in yellow envelopes in class. Remember the proper orientation of the LEDs (long leg toward higher voltage). The micro:bit is going to provide the 3.3V and GND for this circuit. _Note: Because of lower voltage, the lower-resistance, say 330 Ohms, resistors should be used. The LEDs will shine brigher this way._
   2. Copy and paste the JavaScript version of the embedded MakeCode program and program your micro:bit. Test its operation.
   3. Record a brief video of your circuit setup and its operation and link it in the [demo video](#demo-video) section at the bottom of this [README](README.md).
   4. Commit the JavaScript file to your assignment repository, calling it `tutorial.js`.

#### 3. Screensaver extension
1. Reconfigure the circuit and rewrite the program to avoid disabling the LED matrix. 
   1. Choose the correct 3 pins from the [micro:bit GPIO function table](https://learn.sparkfun.com/tutorials/microbit-breakout-board-hookup-guide#hardware-overview) to drive the 3 external LEDs. _Hint: The GPIO pins are multi-function. Choose pins that do not control the LED matrix as one of their functions._
   2. Add code to demonstrate that the LED matrix is enabled. For example, show icons or scroll text. It should appear clearly w/o interference.
   3. Record a brief video of your circuit setup and its operation and link it in the [demo video](#demo-video) section at the bottom of this [README](README.md).   
   4. Commit the JavaScript file to your assignment repository, calling it `enable-matrix.js`.
2. Now that you have a 5x5 LED matrix and 3 external LEDs, extend your favorite screensaver program to include the external LEDs into the changing pattern. 
   1. Do something interesting. Try to incorporate the 3 external LEDs in the changing screensaver pattern.
   2. Record a brief video of your circuit setup and its operation and link it in the [demo video](#demo-video) section at the bottom of this [README](README.md).      
   3. Commit the JavaScript file to your assignment repository, calling it `twenty-eight.js`.

## Resources

### micro:bit 

1. [micro:bit lessons](https://makecode.microbit.org/lessons).

2. [micro:bit ideas](https://microbit.org/ideas/).

3. A list of some more [advanced projects](https://www.itpro.co.uk/desktop-hardware/26289/13-top-bbc-micro-bit-projects).

4. The [projects](https://github.com/carlosperate/awesome-microbit#%EF%B8%8F-projects) at the [awesome micro:bit list](https://github.com/carlosperate/awesome-microbit).

5. micro:bit [technical documentation](https://tech.microbit.org/).

### Github

1. Github Tutorial for Beginners ([webpage](https://product.hubspot.com/blog/git-and-github-tutorial-for-beginners)).

2. Github Basics for Mac and Windows ([video](https://www.youtube.com/watch?v=0fKg7e37bQE)).

3. git & Github Crash Course for Beginners ([video](https://www.youtube.com/watch?v=SWYqp7iY_Tc)).

4. Introduction to Github for Beginners ([video](https://www.youtube.com/watch?v=fQLK8Ib_SKk)).

5. About `git` ([webpage](https://git-scm.com/about)).

6. `git` [documentation](https://git-scm.com/doc) (webpage, book, videos, reference manual).

7. [Github markdown cheat sheet](https://github.com/adam-p/markdown-here/wiki/Markdown-Cheatsheet).

### JavaScript

1. Technically, the language which is used side-by-side with Blocks in the Makecode ronment is a subset of [TypeScript](https://makecode.com/language), which itself is a superset of JavaScript (technically, [ECMAScript](https://www.ecma-international.org/ecma-262/10.0/index.html#Title)), with some JS features not implemented in Makecode.

2. The limited [JavaScript mini-tutorial](https://makecode.microbit.org/javascript) in Makecode.

3. Official [TypeScript documentation](https://www.typescriptlang.org/docs/home.html):
   1. TypeScript in 5 min [tutorial](https://www.typescriptlang.org/docs/handbook/typescript-in-5-minutes.html). _Note: You will need to [download](https://www.typescriptlang.org/index.html#download-links) and install an integrated development environment (IDE). The two that I recommend are Visual Studio Code from Microsoft and WebStorm from JetBrains._
   2. The full documentation and reference is under _Handbook_. Bear in mind that you are drinking from the hose. Don't be surprised if not everything is presented in a strictly incremental manner.
   
4. In-browser TypeScript [playground](https://www.typescriptlang.org/play/index.html). Note that micro:bit specific code will not run, but you can still play. _Start making the distinction between a generic multi-purpose programming language (TypeScript) and functionality (packages, libraries, objects, etc.) that is specific to a particular device (micro:bit), though written in the same programming language._

5. A pretty good and very palatable JS tutorial with in-browser coding, by [Codecademy](https://www.codecademy.com/learn/introduction-to-javascript).

6. Extensive and detailed [JS tutorial](https://javascript.info/), with some advanced material thrown in. **I like this one!**

7. The most authoritative JS resource on the Web, including tutorials and reference, by [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript).

---

## Measurements

_TODO: Record and describe your circuit measurements and calculations here._

## Demo videos

_TODO: Add your video descriptions and URLs here. The videos should clearly show the completion of the corresponding task._
