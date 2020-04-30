## Kaleidoscope Example

A Kaleidoscope is usually made of two or more reflective surfaces positioned at a specific angle to each other, surrounded by a cilindric casing. What is seen trhough this tube is a circular and symetrycal pattern obtained from the reflection of the the opposite end of the tube. Based on this concept the idea of this project is to play around with the different variables used to build a Kaleidoscope and see what the result is in real-time.

In order to build the Kaleidoscope example the [Three.js](https://threejs.org/) library will be used. The first thing to do is to build a group of 3D objects, in this case icosahedron geometry, or Mesh and randomly place them within a range. This mesh will also have a simple animation so the Kaleidoscope will constantly be changing. In order to be able to change the color of the object a standard mesh material object will be used. A white light source will be added to enhance the 3D shapes. And finally just to understand what the Kaleidoscope is actually doing the reflection can be deactivated so the original mesh will be visible.

The Kaleidoscope is redrawn every time a change in the configuration variables is detected. These variables are the following:
- The color of the objects
- The number of the objects displayed
- The boundaries range forming a cube where the objects will be randomly placed, e.g. [-10, 10]
- The size of each object
- The number of sides of the kaleidoscope
- The angle in degrees that each side will be placed

[DEMO](https://jaec86.github.io/amt-workshop2/)