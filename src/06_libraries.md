# Libraries
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#section-physics-libraries .section data-type="chapter"}
# Chapter 6. Physics Libraries {#chapter-6-physics-libraries}

::: chapter-opening-quote
> A library implies an act of faith
>
> Which generations still in darkness hid
>
> Sign in their night, in witness of the dawn.
>
> ::: chapter-opening-quote-source
> ---Victor Hugo
> :::
:::

::: chapter-opening-figure
![image](images/06_libraries/06_libraries_1.png)

### Living root bridges (photo by Arshiya Urveeja Bose)

In the Indian state of Meghalaya, the Khasi and Jaiñtia peoples live in
areas that experience some of the highest rainfall in the world. During
the monsoon season, floods often make traveling between villages
impossible. As a result, the ancient tradition of constructing living
root bridges emerged. These bridges, like the double living root bridge
in East Khasi shown here, are created by guiding and growing tree roots
through bamboo, palm trunks, or steel scaffolding. They grow and become
stronger as the roots interact with the environment, forming adaptive,
springlike connections.
:::

Think about what you've accomplished so far in this book. You've done
the following:

1.  Learned about concepts from the world of physics (What is a vector?
    What is a force? What is a wave?)
2.  Understood the math and algorithms behind those concepts
3.  Implemented those algorithms in p5.js with an object-oriented
    approach, culminating in building simulations of autonomous steering
    agents

These activities have yielded a set of motion simulations, allowing you
to creatively define the physics of the worlds you build (whether
realistic or fantastical). But, of course, you and I aren't the first or
only people to do this. The world of computer graphics and programming
is full of prewritten code libraries dedicated to physics simulations.

Just try searching *open source physics engine* and you could spend the
rest of your day poring over a host of rich and complex codebases. This
begs the question: If an existing code library takes care of physics
simulation, why should you bother learning how to write any of the
algorithms yourself? Here's where the philosophy behind this book comes
into play. While many libraries provide out-of-the-box physics to
experiment with (super-awesome, sophisticated, and robust physics at
that), there are several good reasons for learning the fundamentals from
scratch before diving into such libraries.

First, without an understanding of vectors, forces, and trigonometry,
it's easy to get lost just reading the documentation of a library, let
alone using it. Second, even though a library may take care of the math
behind the scenes, it won't necessarily simplify your code. A great deal
of overhead may be required in understanding how a library works and
what it expects from you code-wise. Finally, as wonderful as a physics
engine might be, if you look deep down into your heart, you'll likely
see that you seek to create worlds and visualizations that stretch the
limits of the imagination. A library may be great, but it provides only
a limited set of features. It's important to know when to live within
those limitations in the pursuit of a creative coding project and when
those limits will prove to be confining.

This chapter is dedicated to examining two open source physics libraries
for JavaScript: [Matter.js](https://brm.io/matter-js) and
[Toxiclibs.js](http://haptic-data.com/toxiclibsjs). I don't mean to
imply that these are the only libraries you should use for any and all
creative coding projects that could benefit from a physics engine (see
["Other Physics Libraries"](#other-physics-libraries){.page-reference}
for alternatives, and check the book's website for ports of the
chapter's examples to other libraries). However, both libraries
integrate nicely with p5.js and will allow me to demonstrate the
fundamental concepts behind physics engines and how they relate to and
build upon the material I've covered so far.

Ultimately, the aim of this chapter isn't to teach you the details of a
specific physics library, but to provide you with a foundation for
working with *any* physics library. The skills you acquire here will
enable you to navigate and understand documentation, opening the door
for you to expand your abilities with any library you choose.

## Why Use a Physics Library?

I've made the case for writing your own physics simulations (as you've
learned to do in the previous chapters), but why use a physics library?
After all, adding any external framework or library to a project
introduces complexity and extra code. Is that additional overhead worth
it? If you just want to simulate a circle falling down because of
gravity, for example, do you really need to import an entire physics
engine and learn its API? As the early chapters of this book hopefully
demonstrated, probably not. Lots of scenarios like this are simple
enough for you to get by writing the code yourself.

But consider another scenario. What if you want to have 100 circles
falling? And what if they aren't circles at all, but rather irregularly
shaped polygons? And what if you want these polygons to bounce off one
another in a realistic manner when they collide?

You may have noticed that while I've covered motion and forces in
detail, I've so far skipped over a rather important aspect of physics
simulation: **collisions**. Let's pretend for a moment that you aren't
reading a chapter about physics libraries and that I've decided right
now to explain how to handle collisions in a particle system. I'd have
to cover two distinct algorithms that address these questions:

1.  How do I determine if two shapes are colliding (or intersecting)?
    This is known as **collision detection**.
2.  How do I determine the shapes' velocities after the collision? This
    is known as **collision resolution**.

If you're working with simple geometric shapes, question 1 isn't too
tough. In fact, perhaps you've encountered it before. With two circles,
for instance, you know they're intersecting if the distance between
their centers is less than the sum of their radii (see Figure 6.1).

<figure>
<img src="images/06_libraries/06_libraries_2.png"
alt="Figure 6.1: Two circles with radii r_1 and r_2 are colliding if the distance between them is less than r_1 + r_2." />
<figcaption aria-hidden="true">Figure 6.1: Two circles with radii <span
data-type="equation">r_1</span> and <span
data-type="equation">r_2</span> are colliding if the distance between
them is less than <span data-type="equation">r_1 +
r_2</span>.</figcaption>
</figure>

That's easy enough, but how about calculating the circles' velocities
after the collision? This is where I'm going to stop the discussion.
Why, you ask? It's not that understanding the math behind collisions
isn't important or valuable. (In fact, I'm including additional examples
on the website related to collisions without a physics library.) The
reason for stopping is that life is short! (Let this also be a reason
for you to consider going outside and frolicking for a bit before
sitting down to write your next sketch.) You can't expect to master
every detail of physics simulation. And while you might enjoy learning
about collision resolution for circles, it's only going to make you want
to work with rectangles next. And then with strangely shaped polygons.
And then curved surfaces. And then swinging pendulums colliding with
springy springs. And then, and then, and then . . .

Incorporating complex features like collisions into a p5.js sketch while
still having time to spend with friends and family---that's the reason
for this chapter. People have spent years developing solutions to these
kinds of problems, and beautiful JavaScript libraries like Matter.js and
Toxiclibs.js are the fruits of those efforts. You don't need to reinvent
the proverbial wheel, at least for now.

In conclusion, if you find yourself describing an idea for a p5.js
sketch and the word *collisions* comes up, then it's likely time to
learn to use a physics engine.

:::: allow-break
::: {data-type="note"}
### Other Physics Libraries

A multitude of other physics libraries are worth exploring alongside
this chapter's two case studies, each with unique strengths that may
offer advantages in certain kinds of projects. In fact, when I first
began writing this book, Matter.js didn't exist, so the physics engine I
initially used to demonstrate the examples was Box2D. It was (and likely
still is) the most well-known physics engine of them all.

[Box2D](https://box2d.org/) began as a set of physics tutorials written
in C++ by Erin Catto for the Game Developers Conference in 2006. Since
then, Box2D has evolved into a rich and elaborate open source physics
engine. It's been used for countless projects, most notably highly
successful games such as the award-winning *Crayon Physics* and the
runaway hit *Angry Birds*.

One important feature of Box2D is that it's a true physics engine: it
knows nothing about computer graphics and the world of pixels, and
instead does all its measurements and calculations in real-world units
like meters, kilograms, and seconds. It's just that its "world" (a key
term in Box2D) is a 2D plane with top, bottom, left, and right edges.
You tell it things like "The gravity of the world is 9.81 newtons per
kilogram, and a circle with a radius of 4 meters and a mass of 50
kilograms is located 10 meters above the world's bottom." Box2D will
then tell you things like "One second later, the rectangle is at 5
meters from the bottom; two seconds later, it's 10 meters below," and so
on.

While this provides for an amazingly accurate and robust physics engine
(one that's highly optimized and fast for C++ projects), it also
necessitates lots of complicated code to translate back and forth
between Box2D's physics world and the world you want to draw---the pixel
world of the graphics canvas. This creates a tremendous burden for the
coder. I will, as best I can, continue to maintain a set of
Box2D-compatible examples for this book (there are several JavaScript
ports), but I believe the relative simplicity of working with a library
like Matter.js that is native to JavaScript and uses pixels as the unit
of measurement will make for a more intuitive and friendly bridge from
my p5.js examples.

Another notable library is [p5play](https://p5play.org/), a project
initiated by Paolo Pedercini and currently led by Quinton Ashley that
was specifically designed for game development. It simplifies the
creation of visual objects---known as sprites---and manages their
interactions (namely, collisions and overlaps). As you may have guessed
from the name, p5play is tailored to work seamlessly with p5.js. It uses
Box2D under the hood for physics simulation.
:::
::::

## Importing the Matter.js Library {#importing-the-matterjs-library}

In a moment, I'll turn to working with Matter.js, created by Liam
Brummitt in 2014. But before you can use an external JavaScript library
in a p5.js project, you need to import it into your sketch. As you're
already quite aware, I'm using the official p5.js web editor for
developing and sharing this book's code examples. The easiest way to add
a library is to edit the *index.html* file that's part of every new
p5.js sketch created in the editor.

To do that, first expand the file navigation bar on the left-hand side
of the editor and select *index.html*, as shown in Figure 6.2.

The file includes a series of `<script>` tags inside the HTML tags
`<head>` and `</head>`. This is how JavaScript libraries are referenced
in a p5.js sketch. It's no different from including `sketch.js` or
`particle.js` in the page's `<body>`, only here, instead of keeping and
editing a copy of the JavaScript code, the library is referenced with a
URL of a **content delivery network (CDN)**. This is a type of server
for hosting files. For JavaScript libraries that are used across
hundreds of thousands of web pages accessed by millions upon millions of
users, CDNs need to be pretty good at their job of serving up these
libraries.

<figure>
<img src="images/06_libraries/06_libraries_3.png"
alt="Figure 6.2: Accessing a sketch’s index.html file" />
<figcaption aria-hidden="true">Figure 6.2: Accessing a sketch’s
<em>index.html</em> file</figcaption>
</figure>

You should already see a `<script>` tag referencing the CDN for p5.js
(it may be a later version by the time you are reading this):

``` {.codesplit code-language="html"}
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.js"></script>
```

To use Matter.js, add another `<script>` tag referencing its CDN right
below the one for p5:

``` {.codesplit code-language="html"}
<script src="https://cdnjs.cloudflare.com/ajax/libs/p5.js/1.9.0/p5.js"></script>
<script src="https://cdnjs.cloudflare.com/ajax/libs/matter-js/0.19.0/matter.min.js"></script>
```

At the time of this writing, the most recent version of Matter.js is
`0.19.0`, and that's what I've referenced in this snippet. As Matter.js
updates and new versions are released, it's often a good idea to
upgrade, but by referencing a specific version that you know works with
your sketch, you don't have to worry about new features of the library
breaking your existing code.

## Matter.js Overview {#matterjs-overview}

When you use Matter.js (or any physics engine) in p5.js, your code ends
up looking a bit different. Here's a pseudocode generalization of all
the examples in [Chapters 1](/vectors#section-vectors) through
[5](/autonomous-agents#section-autonomous-agents):

**`setup()`**

1.  Create all the objects in the world.

**`draw()`**

1.  Calculate all the forces in the world.
2.  Apply all the forces to the objects ([F = M \\times
    A]{data-type="equation"}).
3.  Update the positions of all the objects based on their acceleration.
4.  Draw all the objects.

By contrast, here's the pseudocode for a Matter.js example:

**`setup()`**

1.  Create all the objects in the world.

**`draw()`**

1.  Draw all the objects.

This, of course, is the allure of a physics engine. I've eliminated all
those painful steps of figuring out how the objects are moving according
to velocity and acceleration. Matter.js is going to take care of this
for me!

While there will be more details to reveal, the good news is that the
simplicity of this pseudocode is an accurate reflection of the overall
process. In this sense, Matter.js is a bit like a magic box. In
`setup()`, I'm going to say to Matter, "Hello there. Here are all of the
things I want in my world." Then, in `draw()`, I'm going to politely ask
Matter, "Oh, hello again. If it's not too much trouble, I'd like to draw
all of those things in my world. Could you please tell me where they
are?"

The bad news: the process is not quite as simple as the pseudocode might
lead you to believe. Actually making the stuff that goes into the
Matter.js world requires several steps related to building and
configuring different kinds of shapes.

::: avoid-break
It's also necessary to learn to speak the language of Matter.js in terms
of how the various forces and other parameters of the world are
configured. Here are the core concepts:

-   **Engine:** The entity that manages the physics simulation itself.
    The engine holds on to the world of the simulation as well as
    various properties indicating how the world is updated over time.
:::

-   **Bodies:** The primary elements in the world, corresponding to the
    physical objects being simulated. A body has a position and a
    velocity. Sound familiar? It's basically another version of the
    class I've been building throughout [Chapters
    1](/vectors#section-vectors) through
    [5](/autonomous-agents#section-autonomous-agents). It also has
    geometry to define its shape. It's important to note that *body* is
    a generic term that physics engines use to describe a *thing* in the
    world (similarly to the term *particle*); it isn't related to an
    anthropomorphic body.
-   **Composite:** A container that allows for the creation of complex
    entities (made up of multiple bodies). The world itself is an
    example of a composite, and every body created has to be added to
    the world.
-   **Constraints:** Act as connections between bodies.

In the coming sections, I'll walk through each of these elements in
detail, building several examples along the way. But first, there's one
other important element to briefly discuss:

-   **Vector:** Describes an entity with magnitude and direction using
    x- and y-components, defining positions, velocities, and forces in a
    Matter.js world.

This brings us to an important crossroads. Any physics library is
fundamentally built around vectors, and depending on how you spin it,
that's either a good thing or a bad thing. The good part is that you've
just spent several chapters familiarizing yourself with what it means to
describe motion and forces with vectors, so there's nothing conceptually
new for you to learn. The bad part---the part that makes a single tear
fall from my eye---is that once you cross this threshold into the brave
new world of physics libraries, you don't get to use `p5.Vector`
anymore.

It's been great that p5.js has a built-in vector representation, but
anytime you use a physics library, you'll likely discover that it
includes its own separate vector implementation, designed to be
especially compatible with the rest of the library's code. This makes
sense. After all, why should Matter.js be expected to know about
`p5.Vector`objects?

The upshot of all this is that while you won't have to learn any new
concepts, you do have to get used to new naming conventions and syntax.
To illustrate, I'll show you some now-familiar `p5.Vector` operations
alongside the equivalent `Matter.Vector` code. First, how do you create
a vector?

+-----------------------------------+-----------------------------------+
| p5.js                             | Matter.js                         |
+===================================+===================================+
|     let v = createVector(1, -1);  |     let                           |
|                                   |  v = Matter.Vector.create(1, -1); |
+-----------------------------------+-----------------------------------+

What about adding two vectors together?

+-----------------------------------+-----------------------------------+
| p5.js                             | Matter.js                         |
+===================================+===================================+
|     let a = createVector(1, -1);  |     let                           |
|     let b = createVector(3, 4);   |  a = Matter.Vector.create(1, -1); |
|     a.add(b);                     |     le                            |
|                                   | t b = Matter.Vector.create(3, 4); |
|                                   |     Matter.Vector.add(a, b, a);   |
+-----------------------------------+-----------------------------------+

That overwrites vector `a` with the result. Here's how to put the result
in a separate vector instead:

+-----------------------------------+-----------------------------------+
| p5.js                             | Matter.js                         |
+===================================+===================================+
|     let a = createVector(1, -1);  |     let                           |
|     let b = createVector(3, 4);   |  a = Matter.Vector.create(1, -1); |
|     let c = p5.Vector.add(a, b);  |     le                            |
|                                   | t b = Matter.Vector.create(3, 4); |
|                                   |                                   |
|                                   |  let c = Matter.Vector.add(a, b); |
+-----------------------------------+-----------------------------------+

How about if you want to scale the vector (multiply by a scalar value)?

+-----------------------------------+-----------------------------------+
| p5.js                             | Matter.js                         |
+===================================+===================================+
|     let v = createVector(1, -1);  |     let                           |
|     v.mult(4);                    |  v = Matter.Vector.create(1, -1); |
|                                   |     v = Matter.Vector.mult(v, 4); |
+-----------------------------------+-----------------------------------+

Magnitude and normalize?

+-----------------------------------+-----------------------------------+
| p5.js                             | Matter.js                         |
+===================================+===================================+
|     let v = createVector(3, 4);   |     le                            |
|     let m = v.mag();              | t v = Matter.Vector.create(3, 4); |
|     v.normalize();                |     le                            |
|                                   | t m = Matter.Vector.magnitude(v); |
|                                   |                                   |
|                                   |   v = Matter.Vector.normalise(v); |
+-----------------------------------+-----------------------------------+

As you can see, the concepts are the same, but the specifics of the code
are different. First, every method name is now preceded by
`Matter.Vector`, which defines the **namespace** of the source code.
This is common for JavaScript libraries; p5.js is unusual for not
consistently using namespaces. For example, to draw a circle in p5.js,
you call `circle()` rather than `p5.circle()`. The `circle()` function
lives in the global namespace. This, in my view, is one of the features
that makes p5.js special in terms of ease of use and beginner
friendliness. However, it also means that for any code you write with
p5.js, you can't use `circle` as a variable name. Namespacing a library
protects against these kinds of errors and naming conflicts, and it's
why you'll see everything in Matter.js called with the `Matter` prefix.

In addition, unlike p5.js's static and nonstatic versions of vector
methods like `add()` and `mult()`, all vector methods in Matter.js are
static. If you want to change a `Matter.Vector` while operating on it,
you can add it as an optional argument: `Matter.Vector.add(a, b, a)`
adds `a` and `b` and places the result in `a` (the third argument). You
can also set an existing variable to the newly created vector object
resulting from a calculation, as in `v = Matter.Vector.mult(v, 2)`.
However, this version still creates a new vector in memory rather than
updating the old one.

I'll cover more of the basics for working with `Matter.Vector` in this
chapter, but for details, you can find the [full documentation on the
Matter.js website](https://brm.io/matter-js).

### Engine

Many physics libraries include a *world* object to manage everything.
The world is typically in charge of the coordinate space, keeping a list
of all the bodies in the simulation, controlling time, and more. In
Matter.js, the world is created inside an `Engine` object, the main
controller of your physics world and simulation:

``` {.codesplit code-language="javascript"}
// An alias for the Matter.js Engine class
let Engine = Matter.Engine;

//{!1} A reference to the Matter.js physics engine
let engine;

function setup() {
  createCanvas(640, 360);
  // Create the Matter.js engine.
  engine = Engine.create();
}
```

Notice that the very first line of code creates an `Engine` variable and
sets it equal to `Matter.Engine`. Here, I'm deciding to point the single
keyword `Engine` to the `Engine` class namespaced inside Matter.js in
order to make my code less verbose. This works because I know I won't be
using the word `Engine` for any other variables, nor does it conflict
with something in p5.js. I'll be doing this with `Vector`, `Bodies`,
`Composite`, and more as I continue to build the examples. (But while
the linked source code will always include all the aliases, I won't
always show them in the book text.)

When you call `create()` on `Engine`, Matter.js returns a new physics
engine and world with a default gravity---a vector (0, 1) pointing down.
You can change this default by accessing the `gravity` variable:

``` {.codesplit code-language="javascript"}
  // Change the engine’s gravity to point horizontally.
  engine.gravity.x = 1;
  engine.gravity.y = 0;
```

Of course, gravity doesn't have to be fixed for the duration of the
simulation; you can adjust the gravity vector while your program is
running. You can also turn gravity off altogether by setting it to a (0,
0) vector.

::: {data-type="note"}
### Object Destructuring

**Object destructuring** in JavaScript is a technique for extracting
properties from an object and assigning them to variables. In the case
of Matter.js, the `Matter` object contains the `Engine` property.
Normally, an alias for this property can be set with
`let Engine = Matter.Engine`, but with destructuring, the alias can be
created more concisely:

``` {.codesplit code-language="javascript"}
const { Engine } = Matter;
```

Hold on. Did you catch that I snuck in a `const` here? I know I said
back in [Chapter 0](/random#section-random) that I would use only `let`
for variable declarations throughout this book. However, working with an
external library is a really good time to dip your toe in the `const`
waters. In JavaScript, `const` is used for declaring variables whose
values should never be reassigned after initialization. In this case, I
want to protect myself from accidentally overwriting the `Engine`
variable later in the code, which would likely break everything!

With that out of the way, let's look at how the destructuring syntax
really shines when you need to create aliases to multiple properties of
the same object:

``` {.codesplit code-language="javascript"}
// Use object destructuring to extract aliases for Engine and Vector.
const { Engine, Vector } = Matter;
```

This sets up `Engine` as an alias for `Matter.Engine`, and `Vector` as
an alias for `Matter.Vector`, all in one statement. I'll use this
technique throughout the chapter's examples.
:::

Once the world is initialized, it's time to put stuff in it---bodies!

### Bodies

The **body** is the primary element in the Matter.js world. It's the
equivalent of the `Vehicle` (née `Particle`, née `Mover`) class I built
in previous chapters---the thing that moves around the space and
experiences forces. A body can also be static (fixed and not moving).

Matter.js bodies are created using factory methods found in
`Matter.Bodies`, with different methods available for creating different
kinds of bodies. A **factory method** is a function that creates an
object. While you're probably more familiar with calling a constructor
to create an object---for example, with `new Particle()`---you've seen
factory methods before: `createVector()` is a factory method for
creating a `p5.Vector` object. Whether an object is created from a
constructor or a factory method is a matter of style and design choice
by a library creator.

All the factory methods for creating bodies can be found in the
`Matter.Bodies` [documentation
page](https://brm.io/matter-js/docs/classes/Bodies.html). I'll start
with the `rectangle()` method:

``` {.codesplit code-language="javascript"}
// Create a Matter.js body with a rectangular shape.
let box = Bodies.rectangle(x, y, w, h);
```

What luck! The `rectangle()` method signature is exactly the same as
p5.js's `rect()` function. In this case, however, the method isn't
*drawing* a rectangle but rather building the geometry for a `Body`
object to store. (Note that calling `Bodies.rectangle()` works only if
you first establish `Bodies` as an alias to `Matter.Bodies`.)

A body has now been created with a position and a size, and a reference
to it is stored in the variable `box`. Bodies have many more properties
that affect their motion, however. For example, density ultimately
determines that body's mass. Friction and restitution (bounciness)
affect how the body interacts when it comes into contact with other
bodies. For most cases, the defaults are sufficient, but Matter.js does
allow you to specify these properties by passing through an additional
argument to the factory method in the form of a JavaScript **object
literal**, a collection of key-value pairs separated by commas and
enclosed in curly brackets:

``` {.codesplit code-language="javascript"}
//{!5} Specify the properties of this body in an object literal.
let options = {
  friction: 0.5,    
  restitution: 0.8,
  density: 0.002  
};
let box = Bodies.rectangle(x, y, w, h, options);
```

Each key in the object literal (for example, `friction`) serves as a
unique identifier, and its value (`0.5`) is the data associated with
that key. You can think of an object literal as a simple dictionary or
lookup table---in this case, holding the desired settings for a new
Matter.js body. Note, however, that while the `options` argument is
useful for configuring the body, other initial conditions, such as
linear or angular velocity, can be set via static methods of the
`Matter.Body` class:

``` {.codesplit code-language="javascript"}
// Set an arbitrary initial linear and angular velocity.
const v = Vector.create(2, 0);
Body.setVelocity(box, v);
Body.setAngularVelocity(box, 0.1);
```

Creating a body and storing it in a variable isn't enough. Any body must
be explicitly added to the world in order for it to be simulated with
physics. The physics world is a `Composite` object called `world` stored
inside the `engine` itself. The `box` can be added to that world with
the static `add()` method:

``` {.codesplit code-language="javascript"}
// Add the box object to the engine’s world.
Composite.add(engine.world, box);
```

This extra step is easy to forget---it's a mistake I've made on
countless occasions. If you're ever wondering why one of your objects
doesn't appear or move along with the world's physics, always check that
you've actually added it to the world!

::: {data-type="exercise"}
### Exercise 6.1 {#exercise-61}

Knowing what you know about Matter.js so far, fill in the blank in the
following code that demonstrates how to make a circular body:

``` {.codesplit code-language="javascript"}
let options = {
  friction: 0.5,    
  restitution: 0.8,
};
let ball = Bodies.circle(x, y, radius, options);
```
:::

### Render

Once a body is added to the world, Matter.js will always know it's
there, check it for collisions, and update its position appropriately,
according to any forces in the environment. It'll do all that without
you having to lift a finger! But how do you draw the body?

In the next section, I'll show you how to query Matter.js for the
position of the various bodies in order to render the world with p5.js.
The way that works is fundamental to being able to control the look of
your own animations. This is your time to shine: you can be the designer
of your world, using your creativity and p5.js skills to visualize the
bodies, while politely asking Matter.js to compute all the physics in
the background.

That said, Matter.js does include a fairly simple and straightforward
`Render` class, which is incredibly useful for quickly seeing and
debugging the world you've designed. It provides ways to customize the
*debug drawing* style, but I find the defaults perfectly adequate for
quickly double-checking that I've configured a world correctly.

The first step is to call `Matter.Render.create()` (or
`Render.create()`, assuming an alias). This method expects an object
with the desired settings for the renderer, which I'll call `params`.

::: avoid-break
``` {.codesplit code-language="javascript"}
// Store the canvas in a variable.
let canvas = createCanvas(640, 360);
// Configure the renderer.
let params = {
  canvas: canvas.elt,
  engine: engine,
  options: { width: width, height: height }
};
// Create the renderer.
let render = Render.create(params);
```
:::

Notice that I'm storing a reference to the p5.js canvas in the `canvas`
variable. This is necessary because I need to tell the renderer to draw
in a specific canvas. Matter.js doesn't know about p5.js, so the canvas
it's assigned is a native HTML5 canvas, stored inside the `elt` property
of a p5.js canvas object. The engine is the `engine` I previously
created. The Matter.js default canvas dimensions are 800×600, so if I
prefer a different size, I need to configure an `options` property with
`width` and `height`.

Once I have a `render` object, I need to tell Matter.js to run it:

``` {.codesplit code-language="javascript"}
// Run the renderer!
Render.run(render);
```

One more critical order of business remains: physics engines must be
told to step forward in time. Since I'm using the built-in renderer, I
can also use the built-in runner, which runs the engine at a default
frame rate of 60 frames per second. The runner is also customizable, but
the details aren't terribly important since the goal here is to move
toward using p5.js's `draw()` loop instead (coming in the next section):

``` {.codesplit code-language="javascript"}
// Run the engine!
Runner.run(engine);
```

Here's the Matter.js code all together, with an added `ground`
object---another rectangular body. Note the use of the
`{ isStatic: true }` option in the creation of the ground body to ensure
that it remains in a fixed position. I'll cover more details about
static bodies in ["Static Matter.js
Bodies"](#static-matterjs-bodies){.page-reference}.

::: {data-type="example"}
### Example 6.1: Matter.js Default Render and Runner {#example-61-matterjs-default-render-and-runner}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/GXRa48IQO"
data-example-path="examples/06_libraries/6_1_default_matter_js">
<img src="examples/06_libraries/6_1_default_matter_js/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
// {!1} Note the use of aliases for all the Matter.js classes needed for this sketch.
const { Engine, Bodies, Composite, Body, Vector, Render } = Matter;

function setup() {
  // Store a reference to the canvas.
  let canvas = createCanvas(640, 360);
  // Create the physics engine.
  let engine = Engine.create();
  // Create a renderer and assign it to the p5.js canvas.
  let render = Render.create({
    canvas: canvas.elt, engine,
    options: { width: width, height: height },
  });
  Render.run(render);
  // Create a box with custom friction and restitution.
  let options = { friction: 0.01, restitution: 0.75 };
  let box = Bodies.rectangle(100, 100, 50, 50, options);
  // Set the initial velocity of the box.
  Body.setVelocity(box, Vector.create(5, 0));
  Body.setAngularVelocity(box, 0.1);
  // Add the box to the world.
  Composite.add(engine.world, box);
  // Create a static body for the ground.
  let ground = Bodies.rectangle(width / 2, height - 5, 
                 width, 10, { isStatic: true });
  Composite.add(engine.world, ground);
  // Create the runner.
  let runner = Matter.Runner.create();
  // Run the engine.
  Matter.Runner.run(runner, engine);
}
```

There's no `draw()` function here, and all the variables are local to
`setup()`. In fact, I'm not using any p5.js capabilities (beyond
injecting a canvas onto the page). This is exactly what I want to tackle
next!

## Matter.js with p5.js {#matterjs-with-p5js}

Matter.js keeps a list of all bodies that exist in the world, and as
you've just seen, it can handle\
drawing and animating them with the `Render` and `Runner` objects. (That
list, incidentally, is stored in `engine.world.bodies`.) What I'd like
to show you now, however, is a technique for keeping your own list(s) of
Matter.js bodies, so you can draw them with p5.js.

Yes, this approach may add redundancy and sacrifice a small amount of
efficiency, but it more than makes up for that with ease of use and
customization. With this methodology, you'll be able to code as you're
accustomed to in p5.js, keeping track of which bodies are which and
drawing them appropriately. Consider the file structure of the sketch
shown in Figure 6.3.

![Figure 6.3: The file structure of a typical p5.js
sketch](images/06_libraries/06_libraries_4.png)

Structurally, this looks like just another p5.js sketch. There's a main
*sketch.js* file, as well as *box.js*. This sort of extra file is where
I'd typically declare a class needed for the sketch---in this case, a
`Box` class describing a rectangular body in the world:

``` {.codesplit code-language="javascript"}
class Box {
  constructor(x, y) {
    //{!3} A box has an (x, y) position and a width.
    this.x = x;
    this.y = y;
    this.w = 16;
  }

  show() {
    //{!5} The box is drawn as a square().
    rectMode(CENTER);
    fill(127);
    stroke(0);
    strokeWeight(2);
    square(this.x, this.y, this.w);
  }
}
```

Now I'll write a *sketch.js* file that creates a new `Box` whenever the
mouse is clicked and stores all the `Box` objects in an array. (This is
the same approach I took in the particle system examples from [Chapter
4](/particles#section-particles).)

::: {data-type="example"}
### Example 6.2: A Comfortable and Cozy p5.js Sketch That Needs a Little Matter.js {#example-62-a-comfortable-and-cozy-p5js-sketch-that-needs-a-little-matterjs}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/D26YvXr_S"
data-example-path="examples/06_libraries/6_2_boxes_exercise">
<img src="examples/06_libraries/6_2_boxes_exercise/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
//{!1} An array to store all Box objects
let boxes = [];

function setup() {
  createCanvas(640, 360);
}

function draw() {
  background(255);
  //{!4} When the mouse is clicked, add a new Box object.
  if (mouseIsPressed) {
    let box = new Box(mouseX, mouseY);
    boxes.push(box);
  }
  //{!3} Display all the Box objects.
  for (let box of boxes) {
    box.show();
  }
}
```

Right now, this sketch draws fixed boxes to the screen. Here's the
challenge: How can I instead draw boxes that experience physics
(calculated with Matter.js) as soon as they appear, while changing the
code as little as possible?

I'll need three steps to accomplish this goal.

### Step 1: Add Matter.js to the p5.js Sketch {#step-1-add-matterjs-to-the-p5js-sketch}

As it stands, the sketch makes no reference to Matter.js. That clearly
needs to change. Fortunately, this part isn't too tough: I've already
demonstrated all the elements needed to build a Matter.js world. (And
don't forget, in order for this to work, make sure the library is
imported in *index.html.*)

First, I need to add aliases for the necessary Matter.js classes and
create an `Engine` object in `setup()`:

``` {.codesplit code-language="javascript"}
//{!3} Aliases for Engine, Bodies, and Composite
const { Engine, Bodies, Composite } = Matter;

//{!1} The engine is now a global variable!
let engine;

function setup() {
  //{!1} Create the engine.
  engine = Engine.create();
}
```

Then, in `draw()`, I need to make sure to call one critical Matter.js
method, `Engine.update()`:

``` {.codesplit code-language="javascript"}
function draw() {
  //{!1} Step the engine forward in time!
  Engine.update(engine);
}
```

The `Engine.update()` method advances the physics world one step forward
in time. Calling it inside the p5.js `draw()` loop ensures that the
physics will update at every frame of the animation. This mechanism
takes the place of the built-in Matter.js `Runner` object I used in
Example 6.1. The `draw()` loop is the runner now!

Internally, when `Engine.update()` is called, Matter.js sweeps through
the world, looks at all the bodies in it, and figures out what to do
with them. Just calling `Engine.update()` on its own moves the world
forward with default settings. However, as with `Render`, these settings
are customizable and documented in the [Matter.js
documentation](https://brm.io/matter-js/docs/classes/Engine.html#method_update).

### Step 2: Link Every Box Object with a Matter.js Body {#step-2-link-every-box-object-with-a-matterjs-body}

I've set up my Matter.js world; now I need to link each `Box` object in
my p5.js sketch with a body in that world. The original `Box` class
includes variables for position and width. What I now want to say is "I
hereby relinquish command of this object's position to Matter.js. I no
longer need to keep track of anything related to position, velocity, or
acceleration. Instead, I need to keep track of only the existence of a
Matter.js body and have faith that the physics engine will do the rest."

::: snip-below
``` {.codesplit code-language="javascript"}
class Box {
  constructor(x, y) {
    this.w = 16;
    //{!1} Instead of any of the usual variables, store a reference to a body.
    this.body = Bodies.rectangle(x, y, this.w, this.w);
    //{!1} Don’t forget to add it to the world!
    Composite.add(engine.world, this.body);
  }
```
:::

I don't need `this.x` and `this.y` position variables anymore. The `Box`
constructor takes in the starting x- and y-coordinates, passes them
along to `Bodies.rectangle()` to create a new Matter.js body, and then
forgets about them. As you'll see, the body itself will keep track of
its position behind the scenes. The body could technically keep track of
its dimensions as well, but since Matter.js stores them as a list of
vertices, it's a bit more convenient to hold onto the width of the
square in the `this.w` variable for when it comes time to draw the box.

### Step 3: Draw the Body

Almost there. Before I introduced Matter.js into the sketch, drawing
`Box` was easy. The object's position was stored in the variables
`this.x` and `this.y`:

``` {.codesplit code-language="javascript"}
  // Draw the object by using square().
  show() {
    rectMode(CENTER);
    fill(127);
    stroke(0);
    strokeWeight(2);
    square(this.x, this.y, this.w);
  }
```

Now that Matter.js manages the object's position, I can no longer use my
own `x` and `y`variables to draw the shape. But fear not! The `Box`
object has a reference to the Matter.js body associated with it, and
that body knows its own position. All I need to do is politely ask the
body, "Pardon me, where are you located?"

``` {.codesplit code-language="javascript"}
let position = this.body.position;
```

Just knowing the position of a body isn't enough, however. The body is a
square, so I also need to know its angle of rotation:

``` {.codesplit code-language="javascript"}
let angle = this.body.angle;
```

Once I have the position and angle, I can render the object by using the
native p5.js `translate()`, `rotate()`, and `square()` functions:

``` {.codesplit code-language="javascript"}
  show() {
    //{!2} I need the body’s position and angle.
    let position = this.body.position;
    let angle = this.body.angle;

    rectMode(CENTER);
    fill(127);
    stroke(0);
    strokeWeight(2);
    push();
    //{!2} Use the position and angle to translate and rotate the square.
    translate(position.x, position.y);
    rotate(angle);
    square(0, 0, this.w);
    pop();
  }
```

It's important to note here that if you delete a `Box` object from the
`boxes` array---perhaps when it moves outside the boundaries of the
canvas or reaches the end of its life span, as demonstrated in [Chapter
4](/particles#section-particles)---you must also explicitly remove the
body associated with that `Box` object from the Matter.js world. This
can be done with a `removeBody()` method on the `Box` class:

``` {.codesplit code-language="javascript"}
  // This function removes a body from the Matter.js world.
  removeBody() {
    Composite.remove(engine.world, this.body);
  }
```

In `draw()`, you would then iterate over the array in reverse, just as
in the particle system examples, and call both `removeBody()` and
`splice()` to delete the object from the Matter.js world and your array
of boxes.

::: {data-type="exercise"}
### Exercise 6.2 {#exercise-62}

Start with the code for Example 6.2 and, using the methodology outlined
in this chapter, add the code to implement Matter.js physics. Delete
bodies that have left the canvas. The result should appear as in this
image. Feel free to be creative in the way you draw the boxes!

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/oIZSHFXXk"
data-example-path="examples/06_libraries/6_2_boxes_solved">
<img src="examples/06_libraries/6_2_boxes_solved/screenshot.png" />
</div>
</figure>
:::

## Static Matter.js Bodies {#static-matterjs-bodies}

In the example I just created, the `Box` objects appear at the mouse
position and fall downward because of the default gravity force. What if
I want to add immovable boundaries to the world that will block the path
of the falling `Box` objects? Matter.js makes this easy with the
`isStatic` property:

``` {.codesplit code-language="javascript"}
// Create a fixed (static) boundary body.
let options = { isStatic: true };
let boundary = Bodies.rectangle(x, y, w, h, options);
```

I'm still creating a body with the `Bodies.rectangle()` factory method,
but setting the `isStatic` property ensures that the body will never
move. I'll incorporate this feature into the solution to Exercise 6.2 by
creating a separate `Boundary` class that links a p5.js rectangle to a
static Matter.js body. For variety, I'll also randomize the dimensions
of each falling box. (See the online code for the changes to the `Box`
class.)

::: {data-type="example"}
### Example 6.3: Falling Boxes Hitting Boundaries {#example-63-falling-boxes-hitting-boundaries}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/WSoUy03ph"
data-example-path="examples/06_libraries/6_3_boxes_and_boundaries">
<img
src="examples/06_libraries/6_3_boxes_and_boundaries/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Boundary {
  constructor(x, y, w, h) {
    //{!4} A boundary is a simple rectangle with x, y, width, and height. 
    this.x = x;
    this.y = y;
    this.w = w;
    this.h = h;
    //{!1} Lock the body in place by setting isStatic to true!
    let options = { isStatic: true };
    this.body = Bodies.rectangle(this.x, this.y, this.w, this.h, options);
    Composite.add(engine.world, this.body);
  }
  
  //{!6} Since the boundary can never move, show() can draw it
  // the old-fashioned way, using the original
  // variables. No need to query Matter.js.  
  show() {
    rectMode(CENTER);
    fill(127);
    stroke(0);
    strokeWeight(2);    
    rect(this.x, this.y, this.w, this.h);
  }
}
```

Static bodies don't incorporate material properties like `restitution`
or `friction`. Make sure you set those in the dynamic bodies in your
world.

## Polygons and Groups of Shapes

Now that I've demonstrated how easy it is to use a primitive shape like
a rectangle or circle with Matter.js, let's imagine that you want to
create a more interesting body, such as the abstract character in Figure
6.4.

![Figure 6.4: A compound body made up of multiple
shapes](images/06_libraries/06_libraries_5.png)

Two strategies can be used to make such complex forms. The generic
`Bodies.polygon()` method can create any regular polygon (pentagon,
hexagon, and so on). Additionally, `Bodies.trapezoid()` makes a
quadrilateral with at least one pair of parallel sides:

``` {.codesplit code-language="javascript"}
// A regular hexagon (six-sided polygon)
let hexagon = Bodies.polygon(x, y, 6, radius);
// A trapezoid
let trapezoid = Bodies.trapezoid(x, y, width, height, slope);
```

A more general-purpose option is `Bodies.fromVertices()`. It builds a
shape from an array of vectors, treating them as a series of connected
vertices. I'll encapsulate this logic in a `CustomShape` class.

::: {data-type="example"}
### Example 6.4: Polygon Shapes {#example-64-polygon-shapes}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/o3-Qpqu2i"
data-example-path="examples/06_libraries/6_4_polygon_shapes">
<img src="examples/06_libraries/6_4_polygon_shapes/screenshot.png" />
</div>
</figure>
:::

::: snip-below
``` {.codesplit code-language="javascript"}
class CustomShape {
  constructor(x, y) {
    //{!6} An array of five vectors
    let vertices = [];
    vertices[0] = Vector.create(-10, -10);
    vertices[1] = Vector.create(20, -15);
    vertices[2] = Vector.create(15, 0);
    vertices[3] = Vector.create(0, 10);
    vertices[4] = Vector.create(-20, 15);
    //{!2} Make a body shaped by the vertices.
    let options = { restitution: 1 };
    this.body = Bodies.fromVertices(x, y, vertices, options);

    Body.setVelocity(this.body, Vector.create(random(-5, 5), 0));
    Body.setAngularVelocity(this.body, 0.1);
    Composite.add(engine.world, this.body);
  }
```
:::

When creating a custom polygon in Matter.js, you must remember two
important details. First, the vertices must be specified in clockwise
order. For instance, Figure 6.5 shows the five vertices used to create
the bodies in Example 6.4. Notice that the example added them to the
`vertices` array in clockwise order from the top left.

![Figure 6.5: Vertices on a custom polygon oriented in clockwise
order](images/06_libraries/06_libraries_6.png)

Second, each shape must be convex, not concave. As shown in Figure 6.6,
a **concave** shape has a surface that curves inward, whereas **convex**
is the opposite. Every internal angle in a convex shape must be 180
degrees or less. Matter.js can work with concave shapes, but you need to
build them out of multiple convex shapes (more about that in a moment).

![Figure 6.6: A concave shape can be drawn with multiple convex
shapes. ](images/06_libraries/06_libraries_7.png)

Since the shape is built out of custom vertices, you can use p5.js's
`beginShape()`, `endShape()`, and `vertex()` functions when it comes
time to actually draw the body. The `CustomShape` class *could* include
an array to store the vertices' pixel positions, relative to (0, 0), for
drawing purposes. However, it's best to query Matter.js for the
positions instead. This way, there's no need to use `translate()` or
`rotate()`, since the Matter.js body stores its vertices as absolute
world positions:

::: snip-above
``` {.codesplit code-language="javascript"}
  show() {
    fill(127);
    stroke(0);
    strokeWeight(2);
    // Start the shape.
    beginShape();
    //{!3} Loop through the body vertices.
    for (let v of this.body.vertices) {
      vertex(v.x, v.y);
    }
    // End the shape, closing it.
    endShape(CLOSE);    
  }
}
```
:::

The Matter.js body stores the array of its vertex positions inside a
`vertices` property. Notice that I can then use a `for...of` loop to
cycle through the vertices between `beginShape()` and `endShape()`.

::: {data-type="exercise"}
### Exercise 6.3 {#exercise-63}

Using `Bodies.fromVertices()`, create your own polygon design (remember,
it must be convex). Some possibilities are shown here.

![ ](images/06_libraries/06_libraries_8.png)
:::

A custom shape built from an array of vertices will get you pretty far.
However, the convex shape requirement does limit the range of
possibilities. The good news is that you can eliminate this restriction
by creating a **compound body** made up of multiple shapes! How about
creating a delicious lollipop with a thin rectangle and a circle on top?

I'll start by creating two individual bodies, one rectangle and one
circle. Then I can join them by putting them in a `parts` array and
passing the array to `Body.create()`:

``` {.codesplit code-language="javascript"}
// Make the bodies.
let part1 = Bodies.rectangle(x, y, w, h);
let part2 = Bodies.circle(x, y, r);
// Join the two bodies together in an array.
let body = Body.create({ parts: [part1, part2] });
// Add the compound body to the world.
Composite.add(engine.world, body);
```

While this does create a compound body by combining two shapes, the code
isn't quite right. If you run it, you'll see that both shapes are
centered on the same (*x*, *y*) position, as in Figure 6.7.

<figure>
<img src="images/06_libraries/06_libraries_9.png"
alt="Figure 6.7: A rectangle and a circle with the same (x, y) reference point" />
<figcaption aria-hidden="true">Figure 6.7: A rectangle and a circle with
the same (<em>x</em>, <em>y</em>) reference point</figcaption>
</figure>

Instead, I need to offset the center of the circle horizontally from the
center of the rectangle, as in Figure 6.8.

![Figure 6.8: A circle placed relative to a rectangle with a horizontal
offset](images/06_libraries/06_libraries_10.png)

I'll use half the width of the rectangle as the offset, so the circle is
centered on the edge of the rectangle:

``` {.codesplit code-language="javascript"}
let part1 = Bodies.rectangle(x, y, w, h);
//{!2} Add an offset from the x-position of the lollipop’s stick.
let offset = w / 2;
let part2 = Bodies.circle(x + offset, y, r);
```

Because the lollipop's body has two parts, drawing it is a bit trickier.
I could take multiple approaches. For example, I could use the body's
`vertices` array and draw the lollipop as a custom shape, much like
Example 6.4. (Every body stores an array of vertices, even if it wasn't
created with the `fromVertices()` method.) Since each part of the
lollipop is a primitive shape, however, I'd prefer to separately
translate to each part's position and rotate by the collective body's
angle.

::: {data-type="example"}
### Example 6.5: Multiple Shapes on One Body {#example-65-multiple-shapes-on-one-body}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/xxYF4I5bi"
data-example-path="examples/06_libraries/6_5_compound_bodies">
<img src="examples/06_libraries/6_5_compound_bodies/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
  show() {
    // The angle comes from the compound body.
    let angle = this.body.angle;
    //{!2} Get the position for each part.
    let position1 = this.part1.position;
    let position2 = this.part2.position;
    fill(200);
    stroke(0);
    // Translate and rotate the rectangle (part1).
    push();
    translate(position1.x, position1.y);    
    rotate(angle);
    rectMode(CENTER);
    rect(0, 0, this.w, this.h);
    pop();
    // Translate and rotate the circle (part2).
    push();
    translate(position2.x, position2.y);    
    rotate(angle);
    circle(0, 0, this.r * 2);
    pop();
  }
```

Before moving on, I want to stress that what you draw in your canvas
window doesn't magically experience perfect physics just by the mere act
of creating Matter.js bodies. The chapter's examples have worked because
I've been carefully matching the way I've drawn each p5.js body with the
way I've defined the geometry of each Matter.js body. If you
accidentally draw a shape differently, you won't get an error---not from
p5.js or from Matter.js. However, your sketch will look odd, and the
physics won't work correctly because the world you're seeing won't be
aligned with the world as Matter.js understands it.

To illustrate, let me return to Example 6.5. A lollipop is a compound
body consisting of two parts, a rectangle (`this.part1`) and a circle
(`this.part2`). I've been drawing each lollipop by getting the positions
for the two parts separately: `this.part1.position` and
`this.part2.position`. However, the overall compound body also has a
position, `this.body.position`. It would be tempting to use that as the
position for drawing the rectangle, and to figure out the circle's
position manually using an offset. After all, that's how I conceived of
the compound shape to begin with (look back at Figure 6.8):

``` {.codesplit code-language="javascript"}
  show() {
    //{!1} Getting the body position rather than the parts
    let position = this.body.position;
    let angle = this.body.angle;
    push();
    translate(position.x, position.y);   
    rotate(angle);
    rect(0, 0, this.w, this.h);
    circle(0, this.h / 2, this.r * 2); 
    pop();
  }
```

Figure 6.9 shows the result of this change.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/HWeBLcNuu"
data-example-path="examples/06_libraries/6_5_compound_bodies_error">
<img
src="examples/06_libraries/6_5_compound_bodies_error/screenshot.png" />
</div>
<figcaption>Figure 6.9: What happens when the shapes are drawn
differently from their Matter.js configurations</figcaption>
</figure>

At first glance, this new version may look fine, but if you look closer,
the collisions are off and\
the shapes overlap in odd ways. This isn't because the physics is
broken; it's because I'm not communicating properly between p5.js and
Matter.js. It turns out the overall body position isn't the center of
the rectangle, but rather the center of mass between the rectangle and
the circle. Matter.js is calculating the physics and managing collisions
as before, but I'm drawing each body in the wrong place! (In the online
version, you can toggle the correct and incorrect renderings by clicking
the mouse.)

::: {data-type="exercise"}
### Exercise 6.4 {#exercise-64}

Make your own little alien being by using multiple shapes attached to a
single body. Remember, you aren't limited to using the basic
shape-drawing functions in p5.js; you can use images and colors, add
hair with lines, and more. Think of the Matter.js shapes as skeletons
for your original fantastical design!
:::

## Matter.js Constraints {#matterjs-constraints}

A Matter.js **constraint** is a mechanism to connect one body to
another, enabling simulations of swinging pendulums, elastic bridges,
squishy characters, wheels spinning on an axle, and more. Constraints
have three types: distance constraints and revolute constraints, both
managed through the `Constraint` class, and mouse constraints, managed
through the `MouseConstraint` class.

### **Distance Constraints**

::: half-width-right
![Figure 6.10: A constraint is a connection between two bodies at an
anchor point for each body.](images/06_libraries/06_libraries_11.png)
:::

A **distance constraint** is a connection of fixed length between two
bodies, similar to a spring force connecting two shapes in [Chapter
3](/oscillation#section-oscillation). The constraint is attached to each
body at a specified **anchor**, a point relative to the body's center
(see Figure 6.10). Depending on the constraint's stiffness property, the
"fixed" length can exhibit variability, much as a spring can be more or
less rigid.

Defining a constraint uses a similar methodology as creating bodies,
only you need to have two bodies ready to go. Let's assume that two
`Particle` objects each store a reference to a Matter.js body in a
property called `body`. I'll call them `particleA` and `particleB`:

``` {.codesplit code-language="javascript"}
let particleA = new Particle();
let particleB = new Particle();
```

I want to create a constraint between these particles. For that, I need
to define a series of options that determine the constraint's behavior:

-   `bodyA`: The first body that the constraint connects, establishing
    one end of the constraint.
-   `bodyB`: The second body that the constraint connects, forming the
    other end.
-   `pointA`: The position, relative to `bodyA`, where the constraint is
    anchored to the first body.
-   `pointB`: The position, relative to `bodyB`, where the constraint is
    anchored to the second body.
-   `length`: The resting or target length of the constraint. The
    constraint will attempt to maintain this length during the
    simulation.
-   `stiffness`: A value from 0 to 1 that represents the rigidity of the
    constraint, with 1 being fully rigid and 0 being completely soft.

These settings get packaged up in an object literal:

::: avoid-break
``` {.codesplit code-language="javascript"}
let options = {
  bodyA: particleA.body,
  bodyB: particleB.body,
  pointA: Vector.create(0, 0),
  pointB: Vector.create(0, 0),
  length: 100,
  stiffness: 0.5
};
```
:::

Technically, the only required options are `bodyA` and `bodyB`, the two
bodies connected by the constraint. If you don't specify any additional
options, Matter.js will choose defaults for the other properties. For
example, it will use `(0, 0)` for each relative anchor point (the body's
center), set the `length` to the current distance between the bodies,
and assign a default `stiffness` of `0.7`. Two other notable options I
didn't include are `damping` and `angularStiffness`. The `damping`
option affects the constraint's resistance to motion, with higher values
causing the constraint to lose energy more quickly. The
`angularStiffness` option controls the rigidity of the constraint's
angular motion, with higher values resulting in less angular flexibility
between the bodies.

Once the options are configured, the constraint can be created. As
usual, this assumes another alias---`Constraint` is equal to
`Matter.Constraint`:

``` {.codesplit code-language="javascript"}
let constraint = Constraint.create(options);
//{!1} Don’t forget to add the constraint to the world!
Composite.add(engine.world, constraint);
```

I can include a constraint to a class to encapsulate and manage the
relationships among multiple bodies. Here's an example of a class that
represents a swinging pendulum (mirroring Example 3.11 from [Chapter
3](/oscillation#section-oscillation)).

::: {data-type="example"}
### Example 6.6: Matter.js Pendulum {#example-66-matterjs-pendulum}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/YT6u0GqtH"
data-example-path="examples/06_libraries/6_6_matter_js_pendulum">
<img
src="examples/06_libraries/6_6_matter_js_pendulum/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Pendulum {
  constructor(x, y, len) {
    this.r = 12;
    this.len = len;
    //{!2} Create two bodies, one for the anchor and one for the bob.
    // The anchor is static.
    this.anchor = Bodies.circle(x, y, this.r, { isStatic: true });
    this.bob = Bodies.circle(x + len, y, this.r, { restitution: 0.6 });
    //{!6} Create a constraint connecting the anchor and the bob.
    let options = {
      bodyA: this.anchor,
      bodyB: this.bob,
      length: this.len,
    };
    this.arm = Constraint.create(options);
    //{!3} Add all bodies and constraints to the world.
    Composite.add(engine.world, this.anchor);
    Composite.add(engine.world, this.bob);
    Composite.add(engine.world, this.arm);
  }

  show() {
    fill(127);
    stroke(0);
    //{!2} Draw a line representing the pendulum arm.
    line(this.anchor.position.x, this.anchor.position.y, 
         this.bob.position.x, this.bob.position.y);
    //{!6} Draw the anchor.
    push();
    translate(this.anchor.position.x, this.anchor.position.y);
    rotate(this.anchor.angle);
    circle(0, 0, this.r * 2);
    line(0, 0, this.r, 0);
    pop();
    //{!6} Draw the bob.
    push();
    translate(this.bob.position.x, this.bob.position.y);
    rotate(this.bob.angle);
    circle(0, 0, this.r * 2);
    line(0, 0, this.r, 0);
    pop();
  }
}
```

Example 6.6 uses a default `stiffness` of `0.7`. If you try a lower
value, the pendulum will appear more like a soft spring.

::: {data-type="exercise"}
### Exercise 6.5 {#exercise-65}

Create a simulation of a bridge by using constraints to connect a
sequence of circles (or rectangles) as shown in the following image. Use
the `isStatic` property to lock the endpoints in place. Experiment with
different values to make the bridge more or less springy. The joints
have no physical geometry, so in order for your bridge not to have
holes, spacing between the nodes will be important.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/7U7yrrbNz"
data-example-path="examples/06_libraries/exercise_6_5_bridge">
<img src="examples/06_libraries/exercise_6_5_bridge/screenshot.png" />
</div>
</figure>
:::

### Revolute Constraints

::: half-width-right
![Figure 6.11: A revolute constraint is a connection between two bodies
at a single anchor point, or
hinge.](images/06_libraries/06_libraries_12.png)
:::

Another kind of connection between bodies common to physics engines is a
**revolute joint**. This type of constraint connects two bodies at a
common anchor point, also known as a **hinge** (see Figure 6.11). While
Matter.js doesn't have a separate revolute constraint, you can make one
with a regular `Constraint` of length 0. This way, the bodies can rotate
around a common anchor point.

The first step is to create the connected bodies. For a first example,
I'd like to create a spinning rectangle (akin to a propeller or
windmill) in a fixed position. For this case, I need only one body
connected to a point. This simplifies the code since I don't have to
worry about collisions between the two bodies connected at a hinge.

``` {.codesplit code-language="javascript"}
// Create a body at a given position with width and height.
let body = Bodies.rectangle(x, y, w, h);
Composite.add(engine.world, body);
```

Next, I can create the constraint. With a `length` of `0`, it needs a
`stiffness` of `1`; otherwise, the constraint may not be stable enough
to keep the body connected at the anchor point:

``` {.codesplit code-language="javascript"}
// The constraint connects the body to a fixed (x, y) position with a length of 0 and stiffness of 1.
let options = {
  bodyA: this.body,
  pointB: { x: x, y: y },
  length: 0,
  stiffness: 1,
};
// Create the constraint and add it to the world.
let constraint = Matter.Constraint.create(options);
Composite.add(engine.world, constraint);
```

Putting the code together, I'll write a sketch with a class called
`Windmill` representing a rotating body. The sketch also includes a
`Particle` class for dropping particles onto the windmill.

::: {data-type="example"}
### Example 6.7: Spinning Windmill {#example-67-spinning-windmill}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/D96JFWc3-"
data-example-path="examples/06_libraries/6_7_windmill">
<img src="examples/06_libraries/6_7_windmill/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Windmill {

  constructor(x, y, w, h) {
    this.w = w;
    this.h = h;
    //{!2} The rotating body
    this.body = Bodies.rectangle(x, y, w, h);
    Composite.add(engine.world, this.body);
    //{!8} The revolute constraint
    let options = {
      bodyA: this.body,
      pointB: { x: x, y: y },
      length: 0,
      stiffness: 1,
    };
    this.constraint = Constraint.create(options);
    Composite.add(engine.world, this.constraint);
  }

  show() {
    rectMode(CENTER);
    fill(127);
    stroke(0);
    strokeWeight(2);
    push();
    translate(this.body.position.x, this.body.position.y);
    push();
    rotate(this.body.angle);
    rect(0, 0, this.w, this.h);
    pop();
    //{!1} Draw a stand for the windmill (not part of the physics).
    line(0, 0, 0, height);
    pop();
  }
}
```

Notice the line in this example representing the windmill stand. It
isn't part of the Matter.js physics world, and I never created a body
for it. This illustrates an important point about working with a physics
engine alongside p5.js: you can add elements to the canvas that
contribute to the visual design without affecting the physics, as long
as you don't need those elements to participate in the simulation
itself.

::: {data-type="exercise"}
### Exercise 6.6 {#exercise-66}

Create a vehicle that has revolute joints for its wheels. Consider the
size and positioning of the wheels. How does changing the `stiffness`
property affect their movement?

![image](images/06_libraries/06_libraries_13.png)
:::

### Mouse Constraints

Before I introduce the `MouseConstraint` class, consider the following
question: How do you set the position of a Matter.js body to the mouse
position? More to the point, why would you need a constraint for this?
After all, you have access to the body's position, and you have access
to the mouse's position. What's wrong with assigning one to the other?

``` {.codesplit code-language="javascript"}
body.position.x = mouseX;
body.position.y = mouseY;
```

While this code will move the body, it will also have the unfortunate
result of breaking the physics. Imagine you've built a teleportation
machine that allows you to move instantly from your bedroom to your
kitchen (good for late-night snacking). That's easy enough to imagine,
but now go ahead and rewrite Newton's laws of motion to account for the
possibility of teleportation. Not so easy anymore, is it?

Matter.js has the same problem. If you manually assign the position of a
body, it's like saying, "Teleport that body," and Matter.js no longer
knows how to compute the physics properly. However, Matter.js *does*
allow you to tie a string around your waist and have a friend of yours
stand in the kitchen and drag you there. Replace your friend with your
mouse, and that's what a mouse constraint is.

Imagine that the moment you click the mouse over a shape, the mouse
attaches to that body with a string. Now you can move the mouse around,
and it will drag the body around with it until you release the mouse.
This works in a similar fashion as a revolute joint in that you can set
the length of that "string" to 0, effectively moving a shape with the
mouse.

Before you can attach the mouse, however, you need to create a Matter.js
`Mouse` object that listens for mouse interactions with the p5.js
canvas:

``` {.codesplit code-language="javascript"}
// Aliases for Matter.js Mouse and MouseConstraint
const { Mouse, MouseConstraint } = Matter;
// Need a reference to the p5.js canvas to listen for the mouse
let canvas = createCanvas(640, 240);
// Create a Matter mouse attached to the native HTML5 canvas element.
let mouse = Mouse.create(canvas.elt);
```

Next, use the `mouse` object to create a `MouseConstraint`:

``` {.codesplit code-language="javascript"}
let mouseConstraint = MouseConstraint.create(engine, { mouse });
Composite.add(engine.world, mouseConstraint);
```

This will instantly allow you to interact with all Matter.js bodies via
the mouse. You don't need to explicitly attach the constraint to a
particular body; any body you click will be constrained to the mouse.

You can also configure all the usual constraint variables by adding a
`constraint` property to the options passed into the
`MouseConstraint.create()` method:

``` {.codesplit code-language="javascript"}
mouse = Mouse.create(canvas.elt);
let options = {
  mouse,
  //{!1} Customize the constraint with additional properties.
  constraint: { stiffness: 0.7 }
};
mouseConstraint = MouseConstraint.create(engine, options);
Composite.add(engine.world, mouseConstraint);
```

Here's an example demonstrating a `MouseConstraint` with two `Box`
objects. Static bodies act as walls around the borders of the canvas.

::: {data-type="example"}
### Example 6.8: MouseConstraint Demonstration {#example-68-mouseconstraint-demonstration}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/mTRKgn44p"
data-example-path="examples/06_libraries/6_8_mouse_constraint">
<img src="examples/06_libraries/6_8_mouse_constraint/screenshot.png" />
</div>
</figure>
:::

In this example, you'll see that the `stiffness` property of the
constraint is set to `0.7`, giving a bit of elasticity to the imaginary
mouse string. Other properties such as `angularStiffness` and `damping`
can also influence the mouse's interaction. What happens if you adjust
these values?

## Adding More Forces

In [Chapter 2](/forces#section-forces), I covered how to build an
environment with multiple forces at play. An object might respond to
gravitational attraction, wind, air resistance, and so on. Clearly,
forces are at work in Matter.js as rectangles and circles spin and fly
around the screen! But so far, I've demonstrated how to manipulate only
a single global force: gravity.

``` {.codesplit code-language="javascript"}
  let engine = Engine.create();
  // Change the engine’s gravity to point horizontally.
  engine.gravity.x = 1;
  engine.gravity.y = 0;
```

If I want to use any of the [Chapter 2](/forces#section-forces)
techniques with Matter.js, I need look no further than the trusty
`applyForce()` method, which I wrote as part of the `Mover` class. It
received a vector, divided it by mass, and accumulated it into the
mover's acceleration. With Matter.js, the same method exists, so I no
longer need to write all the details myself! I can call it with the
static `Body.applyForce()`. Here's what that looks like in what's now
the `Box` class:

``` {.codesplit code-language="javascript"}
class Box {
  applyForce(force) {
    //{!1} Call Body’s applyForce().
    Body.applyForce(this.body, this.body.position, force);
  }
}
```

Here, the `Box` class's `applyForce()` method receives a force vector
and simply passes it along to Matter.js's `applyForce()` method to apply
it to the corresponding body. The key difference with this approach is
that Matter.js is a more sophisticated engine than the examples from
[Chapter 2](/forces#section-forces). The earlier examples assumed that
the force was always applied at the mover's center. Here, I've specified
the exact position on the body where the force is applied. In this case,
I've just applied it to the center as before by asking the body for its
position, but this could be adjusted---for example, a force pushing at
the edge of a box, causing it to spin across the canvas, much like dice
tumbling when thrown.

How can I bring forces into a Matter.js-driven sketch? Say I want to use
a gravitational attraction force. Remember the code from Example 2.6 in
the `Attractor` class?

``` {.codesplit code-language="javascript"}
  attract(mover) {
    let force = p5.Vector.sub(this.position, mover.position);
    let distance = force.mag();
    distance = constrain(distance, 5, 25);
    let strength = (G * this.mass * mover.mass) / (distance * distance);
    force.setMag(strength);
    return force;
  }
```

I can rewrite the exact same method by using `Matter.Vector` and
incorporate it into a new `Attractor` class.

::: {data-type="example"}
### Example 6.9: Attraction with Matter.js {#example-69-attraction-with-matterjs}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/16sblEvax"
data-example-path="examples/06_libraries/6_9_matter_js_attraction">
<img
src="examples/06_libraries/6_9_matter_js_attraction/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Attractor {
  constructor(x, y) {
    // {!3} The attractor is a static Matter.js body.
    this.radius = 32;
    this.body = Bodies.circle(x, y, this.radius, { isStatic: true });
    Composite.add(engine.world, this.body);
  }

  attract(mover) {
    //{!2} The attract() method now uses Matter.js vector functions.
    let force = Vector.sub(this.body.position, mover.body.position);
    let distance = Vector.magnitude(force);
    distance = constrain(distance, 5, 25);

    //{!1} Use a small value for G to keep the system stable.
    let G = 0.02;
    //{!1} The mover’s mass is included here, but the attractor’s mass is left out since, as a static body, it is equivalent to infinity.
    let strength = (G * mover.body.mass) / (distance * distance);
    //{!2} More Matter.js vector functions
    force = Vector.normalise(force);
    force = Vector.mult(force, strength);
    return force;
  }
}
```

In addition to writing a custom `attract()` method for Example 6.9, two
other key elements are required for the sketch to behave more like the
example from [Chapter 2](/forces#section-forces). First, remember that a
Matter.js `Engine` has a default gravity pointing down. I need to
disable it in `setup()` with a `(0, 0)` vector:

``` {.codesplit code-language="javascript"}
engine = Engine.create();
//{!1} Disable the default gravity.
engine.gravity = Vector.create(0, 0);
```

Second, bodies in Matter.js are created with a default air resistance
that causes them to slow down as they move. I need to set this to `0` as
well to simulate the bodies being in the vacuum of space:

::: snip-below
``` {.codesplit code-language="javascript"}
class Mover {
  constructor(x, y, radius) {
    this.radius = radius;
    //{!1} Disable the default air resistance.
    let options = { frictionAir: 0 };
    this.body = Bodies.circle(x, y, this.radius, options);
  }
```
:::

This is also a good time to revisit the concept of mass. Although I'm
accessing the `mass` property of the body associated with the mover in
the `attract()` method, I never explicitly set it. In Matter.js, the
mass of a body is automatically calculated based on its size (area) and
density. Larger bodies will therefore have a greater mass. To increase
the mass relative to the size, you can try setting a `density` property
in the `options` object (the default is `0.001`). For static bodies,
such as the attractor, the mass is considered infinite. This is how the
attractor stays locked in position despite the movers continuously
knocking into it.

::: {data-type="exercise"}
### Exercise 6.7 {#exercise-67}

Incorporate `Body.applyForce()` into a new `spin()` method for Example
6.7's `Windmill` class to simulate a motor continuously rotating the
windmill.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/cN6zF325F"
data-example-path="examples/06_libraries/exercise_6_7_windmill_motor">
<img
src="examples/06_libraries/exercise_6_7_windmill_motor/screenshot.png" />
</div>
</figure>
:::

::: {data-type="exercise"}
### Exercise 6.8 {#exercise-68}

Convert any of the steering behavior examples from [Chapter
5](/autonomous-agents#section-autonomous-agents) to Matter.js. What does
flocking look like with collisions?
:::

## Collision Events

This book isn't called *The Nature of Matter.js*, so I'm not going to
cover every possible feature of the Matter.js library. At this point,
I've gone over the basics of creating bodies and constraints, and shown
you some of what the library can do. With the skills you've gained,
hopefully the learning process will be considerably less painful when it
comes time to use an aspect of Matter.js that I haven't addressed here.
Before moving on, however, one more feature of the library is worth
covering: collision events.

Here's a question you've likely been wondering about: "What if I want
something extra to happen when two bodies collide? I mean, don't get me
wrong---I'm thrilled that Matter.js is handling all the collisions
behind the scenes. But if it's taking care of the collisions for me, how
am I supposed to know when they're happening?"

Your first thoughts to answer this question might be as follows: "Well,
I know all the bodies in the system, and I know where they're all
located. I can just start comparing the bodies' positions and see which
ones are intersecting. Then I can do something extra for the bodies that
are determined to be colliding."

That's a nice thought, but hello? The whole point of using a physics
engine like Matter.js is that it will take care of all that work for
you. If you're going to implement the computational geometry algorithms
to test for intersection, you're basically implementing your own
Matter.js!

Of course, wanting to know when bodies are colliding is a pretty common
problem, so Matter.js has anticipated it. It can alert you to moments of
collision with an **event listener**. If you've worked with mouse or
keyboard interaction in p5.js, you already have experience with event
listeners. Consider the following:

``` {.codesplit code-language="javascript"}
// A mousePressed event you’ve probably written many times before
function mousePressed() {
  print("The mouse was pressed!");
}
```

The global `mousePressed()` function in p5.js is executed whenever the
mouse is clicked. This is known as a **callback**, a function that's
called back at a later time when an event occurs. Matter.js collision
events operate in a similar fashion. Instead of p5.js just knowing to
look for a function called `mousePressed()` when a mouse event occurs,
however, you have to explicitly define the name for a Matter.js
collision callback:

``` {.codesplit code-language="javascript"}
Matter.Events.on(engine, 'collisionStart', handleCollisions);
```

This code specifies that a function named `handleCollisions` should be
executed whenever a collision between two bodies starts. Matter.js also
has events for `'collisionActive'` (executed over and over for the
duration of an ongoing collision) and `'collisionEnd'` (executed when
two bodies stop colliding), but for a basic demonstration, knowing when
the collision begins is more than adequate.

Just as `mousePressed()` is triggered when the mouse is clicked,
`handleCollisions()` (or whatever you choose to name the callback
function) is triggered when two shapes collide. It can be written as
follows:

::: avoid-break
``` {.codesplit code-language="javascript"}
function handleCollisions(event) {

}
```
:::

Notice that the function includes an `event` parameter. This is an
object that includes all the data associated with a collision (or
multiple collisions if more than one has occurred in that time step),
such as which bodies are involved. Matter.js automatically creates this
object and passes it along to the `handleCollisions()` callback every
time a collision occurs.

Say I have a sketch of `Particle` objects. Each stores a reference to a
Matter.js body, and I want the particles to change color when they
collide. Here's the process to follow to make that happen.

**Step 1: Event, could you tell me which two things collided?**

Now, what has collided here? Matter.js detects collisions between a pair
of bodies. Any pair of colliding bodies will be in an array called
`pairs` inside the `event` object. Inside `handleCollisions()`, I can
use a `for...of` loop to iterate over those pairs:

``` {.codesplit code-language="javascript"}
for (let pair of event.pairs) {

}
```

**Step 2: Pair, could you tell me which two bodies you include?**

Each pair in the `pairs` array is an object with references to the two
bodies involved in the collision, `bodyA` and `bodyB`. I'll extract
those bodies:

``` {.codesplit code-language="javascript"}
for (let pair of event.pairs) {
  let bodyA = pair.bodyA;
  let bodyB = pair.bodyB;
}
```

**Step 3: Bodies, could you tell me which particles you're associated
with?**

Getting from the relevant Matter.js bodies to the `Particle` objects
they're associated with is a little harder. After all, Matter.js doesn't
know anything about my code. Sure, it's doing all sorts of stuff to keep
track of the relationships between bodies and constraints, but it's up
to me to manage my own objects and their associations with Matter.js
elements. That said, every Matter.js body is instantiated with an empty
object---`{ }`---called `plugin`, ready to store any custom data about
that body. I can link the body to a custom object (in this case, a
`Particle`) by storing a reference to that object in the `plugin`
property.

Take a look at the updated constructor in the `Particle` class where the
body is made. Note that the body-making procedure has been expanded by
one line of code to add a `particle` property inside `plugin`. It's
important to make sure you're adding a new property to the existing
`plugin` object (in this case, `plugin.particle = this`) rather than
overwriting the `plugin` object (for example, with `plugin = this`). The
latter could interfere with other features or customizations.

::: {.snip-below .avoid-break}
``` {.codesplit code-language="javascript"}
class Particle {

  constructor(x, y, radius) {
    this.radius = radius;
    this.body = Bodies.circle(x, y, this.radius);
    //{!1 .bold} this refers to this Particle object, telling the Matter.js Body to store a
    // reference to this particle that can be accessed later.
    this.body.plugin.particle = this;
    Composite.add(engine.world, this.body);
  }
```
:::

Later, in the `handleCollision()` callback function, that `Particle`
object can be accessed from the `Body` via the `plugin`.

::: {data-type="example"}
### Example 6.10: Collision Events {#example-610-collision-events}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/3cREe4udP"
data-example-path="examples/06_libraries/6_10_collision_events">
<img src="examples/06_libraries/6_10_collision_events/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
function handleCollisions(event) {
  for (let pair of event.pairs) {
    let bodyA = pair.bodyA;
    let bodyB = pair.bodyB;
    //{!2} Retrieve the particles associated with the colliding bodies via the plugin.
    let particleA = bodyA.plugin.particle;
    let particleB = bodyB.plugin.particle;
     //{!4} If they are both particles, change their color!
    if (particleA instanceof Particle && particleB instanceof Particle) {
      particleA.change();
      particleB.change();
    }
  }
}
```

In most cases, you can't assume that the objects that collided are all
`Particle` objects. After all, the particle might have collided with a
`Boundary` object (another kind of thing, depending on what's in your
world). You can check an object's type with JavaScript's `instanceof`
operator, as I've done in this example.

::: {data-type="exercise"}
### Exercise 6.9 {#exercise-69}

Create a simulation in which `Particle` objects disappear when they
collide with one another. Where and how should you delete the particles?
Can you have them shatter into smaller particles?
:::

## A Brief Interlude: Integration Methods

Has this ever happened to you? You're at a fancy cocktail party,
regaling your friends with tall tales of your incredible software
physics simulations. Suddenly, out of the blue, someone pipes up:
"Enchanting! But what integration method are you using?"

*What?!* you think to yourself. *Integration?*

Maybe you've heard the term before. Along with differentiation, it's one
of the two main operations in calculus. Oh right, calculus.

I've managed to get most of the way through this material related to
physics simulation without really needing to dive into calculus. As I
wrap up the first half of this book, however, it's worth taking a moment
to examine the calculus behind what I've been demonstrating and how it
relates to the methodology in certain physics libraries (like Box2D,
Matter.js, and the upcoming Toxiclibs.js). This way, you'll know what to
say at the next cocktail party when someone asks you about integration.

I'll begin with a question: "What does integration have to do with
position, velocity, and acceleration?" To answer, I should first define
**differentiation**, the process of finding a derivative. The
**derivative** of a function is a measure of how a function changes over
time. Consider position and its derivative. Position is a point in
space, while velocity is the change in position over time. Therefore,
velocity can be described as the derivative of position. And what's
acceleration? The change in velocity over time. Acceleration is the
derivative of velocity.

**Integration**, the process of finding an integral, is the inverse of
differentiation. For example, the **integral** of an object's velocity
over time tells us the object's new position when that time period ends.
Position is the integral of velocity, and velocity is the integral of
acceleration.

Since the physics simulations in this book are founded on the notion of
calculating acceleration based on forces, integration is needed to
figure out the object's location after a certain period of time (like
one cycle of the `draw()` loop). In other words, you've been doing
integration all along!

``` {.codesplit code-language="javascript"}
velocity.add(acceleration);
position.add(velocity);
```

This methodology is known as **Euler integration**, or the Euler method
(named for the mathematician Leonhard Euler, pronounced *Oiler*). It's
essentially the simplest form of integration and is very easy to
implement in code---just two lines! However, while it's computationally
simple, it's by no means the most accurate or stable choice for certain
types of simulations.

Why is Euler inaccurate? Think about it this way: when you bounce down a
sidewalk on a pogo stick, does the pogo stick sit in one position at
time equals 1 second, then disappear and suddenly reappear in a new
position at time equals 2 seconds, and do the same thing for 3 seconds,
and 4, and 5? No, of course not. The pogo stick moves continuously
through time.

But what's happening in a p5.js sketch? A circle is at one position at
frame 0, another at frame 1, another at frame 2, and so on. Sure, at 30
frames per second, you see the *illusion* of motion. But a new position
is computed only every *N* units of time, whereas the real world is
perfectly continuous. This results in some inaccuracies, as shown in
Figure 6.12.

![Figure 6.12: The Euler approximation of a
curve](images/06_libraries/06_libraries_14.png)

The "real world" is the smooth curve; the Euler simulation is the series
of straight-line segments. One option to improve on Euler is to use
smaller time steps---instead of once per frame, you could recalculate an
object's position 20 times per frame. But this isn't practical; the
sketch might then run too slowly.

I still believe that Euler is the best method for learning the basics,
and it's also perfectly adequate for most of the projects you might want
to make with p5.js. Anything lost in efficiency or inaccuracy is made up
for in ease of use and understandability. For better accuracy, for
example, the Box2D engine uses symplectic Euler, or semi-explicit Euler,
a slight modification of Euler. Other engines use an integration method
called Runge-Kutta (named for German mathematicians Carl Runge and
Martin Kutta).

Another popular integration method used in physics libraries, including
both Matter.js and Toxiclibs.js, is **Verlet integration**. A simple way
to describe Verlet integration is to think of the typical motion
algorithm without explicitly storing velocity. After all, you don't
really need to store the velocity; if you always know where an object
was at one point in time and where it is now, you can extrapolate its
velocity. Verlet integration does precisely this, calculating velocity
on the fly while the program is running, instead of maintaining a
separate velocity variable.

Verlet integration is particularly well suited for particle systems,
especially those with spring connections between the particles. Physics
libraries hide the details from you so you don't have to worry about how
it all works, but if you're interested in diving deeper into Verlet
physics, I suggest reading the seminal paper on the topic, from which
just about every Verlet computer graphics simulation is derived:
["Advanced Character Physics" by Thomas
Jakobsen](https://www.cs.cmu.edu/afs/cs/academic/class/15462-s13/www/lec_slides/Jakobsen.pdf).

## Verlet Physics with Toxiclibs.js {#verlet-physics-with-toxiclibsjs}

Around 2005, Karsten Schmidt began work on Toxiclibs, a sweeping and
pioneering open source library for computational design, specifically
built for the Java version of Processing. Though it hasn't been actively
maintained in more than 10 years, the concepts and techniques that the
library demonstrated can be found in countless creative coding projects
today. Its website described it as follows:

> *Toxiclibs is an independent, open source library collection for
> computational design tasks with Java and Processing developed by
> Karsten "toxi" Schmidt. The classes are purposefully kept fairly
> generic in order to maximize reuse in different contexts ranging from
> generative design, animation, interaction/interface design, data
> visualization to architecture and digital fabrication, use as teaching
> tool and more.*

Schmidt continues to contribute to the creative coding field through his
recent project, [thi.ng umbrella](https://thi.ng/umbrella). This work
can be considered an indirect successor to Toxiclibs, but with a much
greater scope and detail. If you like this book, you might especially
enjoy exploring [thi.ng vectors](https://thi.ng/vectors), which provides
more than 800 vector algebra functions using plain-vanilla JavaScript
arrays.

While thi.ng/umbrella may be a more modern and sophisticated approach,
Toxiclibs remains a versatile tool, and I continue to use a version
compatible with the latest version of Processing (4.3 as of the time of
this writing). For this book, we should thank our lucky stars for
Toxiclibs.js, a JavaScript adaptation of the library, created by Kyle
Phillips (hapticdata). I'm going to cover only a few examples related to
Verlet physics, but Toxiclibs.js includes a suite of other packages with
functionality related to color, geometry, math, and more.

The examples I'm about to demonstrate could also be created using
Matter.js, but I've decided to move to Toxiclibs.js for several reasons.
The library holds a special place in my heart as a personal favorite,
and it's historically significant. I also believe that showing more than
one physics library is important for providing a broader understanding
of the tools and approaches available.

This switch from Matter.js to Toxiclibs.js raises an important question,
though: How should you decide which library to use for a project?
Matter.js, or Toxiclibs.js, or something else? If you fall into one of
the following two categories, your decision is a bit easier:

-   **My project involves collisions. I have circles, squares, and other
    strangely shaped objects that knock each other around and bounce off
    each other.** In this case, you're going to want to use Matter.js,
    since Toxiclibs.js doesn't handle rigid-body collisions.
-   **My project involves lots of particles flying around the screen.
    Sometimes they attract each other. Sometimes they repel each other.
    And sometimes they're connected with springs.** In this case,
    Toxiclibs.js is likely your best choice. It's simpler to use in some
    ways than Matter.js and particularly well suited to connected
    systems of particles. It's also high performance, because it gets to
    ignore all of the collision geometry.

Here's a little chart that covers some of the features for each physics
library:

  Feature                                     Matter.js   Toxiclibs.js
  ------------------------------------------- ----------- --------------
  Rigid-body collisions                       Yes         No
  3D physics                                  No          Yes
  Particle attraction and repulsion forces    No          Yes
  Spring connections (force based)            Yes         Yes
  Constraints (general-purpose connections)   Yes         No

All the documentation and downloads for the library files can be found
at the [Toxiclibs.js website](http://haptic-data.com/toxiclibsjs). For
the examples in this book, I'll be working with a hosted CDN version of
the library referenced in *index.html*, just as I demonstrated earlier
for Matter.js. Here's the `<script>` element to add:

``` {.codesplit code-language="html"}
<script src="https://cdn.jsdelivr.net/gh/hapticdata/toxiclibsjs@0.3.2/build/toxiclibs.js"></script>
```

My overview of Matter.js focused on a few key features of that library:
world, vector, body, constraint. This has given you a head start on
understanding Toxiclibs.js as well, since it follows a similar
structure. The following table shows the corresponding Toxiclibs.js
features:

+-----------------------------------+-----------------------------------+
| Matter.js                         | Toxiclibs.js                      |
+===================================+===================================+
|     World                         |     VerletPhysics2D               |
+-----------------------------------+-----------------------------------+
|     Vector                        |     Vec2D                         |
+-----------------------------------+-----------------------------------+
|     Body                          |     VerletParticle2D              |
+-----------------------------------+-----------------------------------+
|     Constraint                    |     VerletSpring2D                |
+-----------------------------------+-----------------------------------+

I'll discuss how some of these features translate to Toxiclibs.js before
putting them together to create some interesting examples.

### Vectors

Here we go again. Remember all that time spent learning the ins and outs
of the `p5.Vector` class? Then remember how you had to revisit all those
concepts with Matter.js and the `Matter.Vector` class? Well, it's time
to do it again, because Toxiclibs.js also includes its own vector
classes. It has one for two dimensions and one for three: `Vec2D` and
`Vec3D`. Both are found in the `toxi.geom` package and can be aliased in
the same manner as `Vector` with Matter.js:

``` {.codesplit code-language="javascript"}
let { Vec2D, Vec3D } = toxi.geom;
```

Once again, Toxiclibs.js vectors are conceptually the same as the p5.js
vectors we know and love, but they have their own style and syntax.
Here's an overview of how some of the basic vector math operations from
`p5.Vector` translate to `Vec2D` (I'm sticking with 2D to match the rest
of this book, but I encourage you to explore 3D vectors as well).

+-----------------------------------+-----------------------------------+
| p5.Vector                         | Vec2D                             |
+===================================+===================================+
|     let a = createVector(1, -1);  |     let a = new Vec2D(1, -1);     |
|     let b = createVector(3, 4);   |     let b = new Vec2D(3, 4);      |
|     a.add(b);                     |     a.addSelf(b);                 |
+-----------------------------------+-----------------------------------+
|     let a = createVector(1, -1);  |     let a = new Vec2D(1, -1);     |
|     let b = createVector(3, 4);   |     let b = new Vec2D(3, 4);      |
|     let c = p5.Vector.add(a, b);  |     let c = a.add(b);             |
+-----------------------------------+-----------------------------------+
|     let a = createVector(1, -1);  |     let a = new Vec2D(1, -1);     |
|     let m = a.mag();              |     let m = a.magnitude();        |
|     a.normalize();                |     a.normalize();                |
+-----------------------------------+-----------------------------------+

Notice in particular that Toxiclibs.js vectors are created by calling
the `Vec2D` constructor with the `new` keyword, rather than by using a
factory method like `Matter.Vector()` or `createVector()`.

### The Physics World

The classes to describe the world and its particles and springs in
Toxiclibs.js are found in `toxi.physics2d.` I'm also going to use a
`Rect` object (to describe a generic rectangle boundary)\
and `GravityBehavior` to apply a global gravity force to the world.
Including `Vec2D`, I now have all the following class aliases:

``` {.codesplit code-language="javascript"}
// The necessary geometry classes: vectors, rectangles
let { Vec2D, Rect } = toxi.geom;
// Alias the important classes from toxi.physics2d.
let { VerletPhysics2D, VerletParticle2D, VerletSpring2D } = toxi.physics2d;
// For the world’s gravity
let { GravityBehavior } = toxi.physics2d.behaviors;
```

The first step is to create the world:

::: snip-below
``` {.codesplit code-language="javascript"}
let physics;

function setup() {
  // Create a Toxiclibs world.
  physics = new VerletPhysics2D();
```
:::

Once I have the `VerletPhysics` world, I can set global properties. For
example, if I want hard boundaries beyond which particles can't travel,
I can provide rectangular bounds:

::: {.snip-above .snip-below}
``` {.codesplit code-language="javascript"}
  physics.setWorldBounds(new Rect(0, 0, width, height));
```
:::

In addition, I can add gravity with the `GravityBehavior` object. A
gravity behavior requires a vector---how strong and in what direction is
the gravity?

::: snip-above
``` {.codesplit code-language="javascript"}
  physics.addBehavior(new GravityBehavior(new Vec2D(0, 0.5)));
}
```
:::

Finally, to calculate the physics of the world and move the world's
objects around, I have to call the world's `update()` method. Typically,
this would happen once per frame in `draw()`:

``` {.codesplit code-language="javascript"}
function draw() {
  //{!1} This is the same as the Matter.js Engine.update().
  physics.update();
}
```

Now all that remains is to populate the world.

### Particles

The Toxiclibs.js equivalent of a Matter.js body---a thing that exists in
the world and experiences physics---is a **particle**, as represented by
the `VerletParticle2D` class. However, unlike Matter.js bodies,
Toxiclibs.js particles don't store geometry. They're just points in
space.

How should I integrate Toxiclibs.js particles into a p5.js sketch? In
the Matter.js examples, I created my own class (called `Particle`) and
included a reference to a Matter.js body:

``` {.codesplit code-language="javascript"}
class Particle {
  constructor(x, y, r) {
    this.body = Bodies.circle(x, y, r);
  }
}
```

This technique was somewhat redundant, since Matter.js keeps track of
the bodies in its world. However, it allowed me to manage which body is
which (and therefore how each body should\
be drawn) without having to rely on iterating through Matter.js's
internal lists. I might take the\
same approach with Toxiclibs.js, making my own `Particle` class that
stores a reference to a `VerletParticle2D` object. This way, I'll be
able to give the particles custom properties and draw\
them however I want. I'd probably write the code as follows:

``` {.codesplit code-language="javascript"}
class Particle {
  constructor(x, y, r) {
    //{!1} A VerletParticle needs an initial (x, y) position, but it has no geometry, so the r is used only for drawing.
    this.particle = new VerletParticle2D(x, y);
    this.r = r;
  }

  show() {
    fill(127);
    stroke(0);
    //{!1} When it comes time to draw the particle, the (x, y) is stored in this.particle.
    circle(this.particle.x, this.particle.y, this.r * 2);
  }
}
```

Looking over this code, you might first notice that drawing the particle
is as simple as grabbing the `x` and `y` properties and using them with
`circle()`. Second, you might notice that this `Particle` class doesn't
do much beyond storing a reference to a `VerletParticle2D` object. This
hints at something important. Think back to the discussion of
inheritance in [Chapter 4](/particles#section-particles), and then ask
yourself: What is a `Particle` object other than an augmented
`VerletParticle2D` object? Why bother making two objects---a `Particle`
and a `VerletParticle2D`---for every one particle in the world, when I
could simply extend the `VerletParticle2D` class to include the extra
code needed to draw the particle?

``` {.codesplit code-language="javascript"}
class Particle extends VerletParticle2D {
  constructor(x, y, r) {
    //{!1} Call super() with (x, y) so the object is initialized properly.
    super(x, y);
    //{!1} Add a variable to track the radius.
    this.r = r;
  }

  //{!1} Augment by adding a show() method.
  show() {
    fill(127);
    stroke(0);
    //{!1} x and y from VerletParticle2D!
    circle(this.x, this.y, this.r * 2);
  }
}
```

Furthermore, at the risk of blowing your mind, it turns out that the
`VerletParticle2D` class is a subclass of `Vec2D`. This means that in
addition to inheriting everything from `VerletParticle2D`, the
`Particle` class has inherited all the`Vec2D` methods as well!

I can now create new particles:

``` {.codesplit code-language="javascript"}
let particle = new Particle(width / 2, height / 2, 8);
```

Just creating a particle isn't enough, however. Just as in Matter.js, I
have to explicitly add the new particle to the world. In Toxiclibs.js,
this is done with the `addParticle()` method:

``` {.codesplit code-language="javascript"}
physics.addParticle(particle);
```

If you look at the Toxiclibs.js documentation, you'll see that
`addParticle()` expects a `VerletParticle2D` object. But I've passed it
a `Particle` object. Does that work?

Yes! Remember one of the tenets of OOP: polymorphism. Here, because the
`Particle` class extends `VerletParticle2D`, I can treat the particle in
two ways: as a `Particle` or as a `VerletParticle2D`. This is an
incredibly powerful feature of OOP. If you build custom classes that
inherit from Toxiclibs.js classes, you can use the objects of those
classes in conjunction with all the methods Toxiclibs.js has to offer.

### Springs

In addition to the `VerletParticle2D` class, Toxiclibs.js has a set of
classes that allow you to connect particles with spring forces.
Toxiclibs.js has three types of springs:

-   `VerletSpring2D`: A springy connection between two particles. The
    spring's properties can be configured in such a way as to create a
    stiff, stick-like connection or a highly elastic, stretchy
    connection. A particle can also be locked so that only one end of
    the spring can move.
-   `VerletConstrainedSpring2D`: A spring whose maximum distance can be
    limited. This can help the whole spring system achieve better
    stability.
-   `VerletMinDistanceSpring2D`: A spring that enforces its rest length
    only if the current distance is less than its rest length. This is
    handy if you want to ensure that objects are at least a certain
    distance from each other, but you don't care if the distance is
    bigger than the enforced minimum.

Inheritance and polymorphism once again prove to be useful when making
springs. A spring expects two `VerletParticle2D` objects when it's
created, but as before, two `Particle` objects will do, since `Particle`
extends **** `VerletParticle2D`.

Here's some example code to create a spring. This snippet assumes the
existence of two particles, `particle1` and `particle2`, and creates a
connection between them with a given rest length and strength.

``` {.codesplit code-language="javascript"}
//{!1} What is the rest length of the spring?
let length = 80;
//{!1} How strong is the spring? (The higher the value, the more rigid the spring.)
let strength = 0.01;
let spring = new VerletSpring2D(particle1, particle2, length, strength);
```

Just as with particles, in order for the connection to be part of the
physics world, it must be explicitly added to the world:

``` {.codesplit code-language="javascript"}
physics.addSpring(spring);
```

I have almost everything I need to build a simple first Toxiclibs.js
example: two particles connected to form a springy pendulum. I want to
add one more element, however: mouse interactivity.

With Matter.js, I explained that the physics simulation breaks down if
you manually override a body's position by setting it to the mouse. With
Toxiclibs.js, this isn't a problem. If I want to, I can set a particle's
(*x*, *y*) position manually. However, before doing so, it's generally a
good idea to call the particle's `lock()` method, which fixes the
particle in place. This is identical to setting the `isStatic` property
to `true` in Matter.js.

The idea is to lock the particle temporarily so it stops responding to
the world's physics, alter its position, and then unlock it (with the
`unlock()` method) so it can start moving again from its new location.
For example, say I want to reposition a particle whenever the mouse is
clicked:

``` {.codesplit code-language="javascript"}
  if (mouseIsPressed) {
    //{!4} First lock the particle, then set the x and y, then unlock() it.
    particle1.lock();
    particle1.x = mouseX;
    particle1.y = mouseY;
    particle1.unlock();
  }
```

And with that, I'm ready to put all these elements together in a simple
sketch with two particles connected by a spring. One particle is
permanently locked in place, and the other can be moved by dragging the
mouse. This example is virtually identical to Example 3.11 from [Chapter
3](/oscillation#section-oscillation).

::: {data-type="example"}
### Example 6.11: Simple Spring with Toxiclibs.js {#example-611-simple-spring-with-toxiclibsjs}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/CSzXIfoWH"
data-example-path="examples/06_libraries/6_11_simple_spring_with_toxiclibs">
<img
src="examples/06_libraries/6_11_simple_spring_with_toxiclibs/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
let { Vec2D, Rect } = toxi.geom;
let { VerletPhysics2D, VerletParticle2D, VerletSpring2D } = toxi.physics2d;
let { GravityBehavior } = toxi.physics2d.behaviors;

let physics;
let particle1, particle2;

function setup() {
  createCanvas(640, 240);
  // Create a Toxiclibs.js Verlet physics world.
  physics = new VerletPhysics2D();
  physics.setWorldBounds(new Rect(0, 0, width, height));
  physics.addBehavior(new GravityBehavior(new Vec2D(0, 0.5)));
  //{!1} What is the rest length of the spring?
  let length = 120;
  // Create two particles.
  particle1 = new Particle(width / 2, 0, 8);
  particle2 = new Particle(width / 2 + length, 0, 8);
  // Lock particle 1 in place.
  particle1.lock();
  // Create one spring.
  let spring = new VerletSpring2D(particle1, particle2, length, 0.01);
  //{!3} Must add everything to the world
  physics.addParticle(particle1);
  physics.addParticle(particle2);
  physics.addSpring(spring);
}

function draw() {
  //{!1} Must update the physics
  physics.update();
  background(255);
  //{!4} Draw everything.
  stroke(0);
  line(particle1.x, particle1.y, particle2.x, particle2.y);
  particle1.show();
  particle2.show();
  //{!6} Move the particle according to the mouse. 
  if (mouseIsPressed) {
    particle2.lock();
    particle2.x = mouseX;
    particle2.y = mouseY;
    particle2.unlock();
  }
}

//{!11} How cute is this simple Particle class?
class Particle extends VerletParticle2D {
  constructor(x, y, r) {
    super(x, y);
    this.r = r;
  }

  show() {
    fill(127);
    stroke(0);
    circle(this.x, this.y, this.r * 2);
  }
}
```

In this example, I'm continuing to visually represent the spring
connecting the particles with a line. Keep in mind, however, that the
behavior of the spring still exists, whether you choose to visually
represent it or not. This can open up creative possibilities. For
instance, you could decide to make the spring invisible or depict it in
a completely different way, perhaps as a series of dots or a shape of
your own invention.

## Soft-Body Simulations

Verlet physics is particularly well suited for a genre of computer
graphics known as soft-body simulation. Unlike the **rigid-body**
simulations of Matter.js, in which hard-edged boxes crash into one
another and retain their shapes, **soft-body** simulations involve
objects that can deform and change shape with physics. Soft bodies allow
for more flexible, fluid, and organic movements. They can stretch,
squish, and jiggle in response to forces and collisions, and they appear
. . . well, soft.

One of the first popular examples of soft-body physics was
*SodaConstructor*, a game created in the early 2000s. Players could
construct and animate custom 2D creatures built out of masses and
springs. Other examples over the years have included games like
*LocoRoco*, *World of Goo*, and more recently, *JellyCar*.

The basic building blocks of soft-body simulations are particles
connected by springs---just like the pair particles in Example 6.11.
Figure 6.13 shows how to configure a network of particle-spring
connections to make various forms.

![Figure 6.13: Soft-body simulation
designs](images/06_libraries/06_libraries_15.png)

::: avoid-break
As the figure shows, a string can be simulated by connecting a line of
particles with springs; a blanket can be simulated by connecting a grid
of particles with springs; and a cute, cuddly, squishy cartoon character
can be simulated with a custom layout of particles connected with
springs. It's not much of a leap from one to another.
:::

### A String

I'll begin by simulating a *soft pendulum*---a bob hanging from a
flexible string instead of a rigid arm. As it happens, Toxiclibs.js
offers a convenient `ParticleString2D` class that creates a string of
particles connected by springs in a single constructor call. However,
for demonstration purposes, I'll create my own particle string by using
an array and a `for` loop. This way, you'll gain a deeper understanding
of the system, enabling you to create your own custom designs beyond a
single string in the future.

First, I need an array of particles. I'll use the same `Particle` class
built in Example 6.11:

``` {.codesplit code-language="javascript"}
let particles = [];
```

Now, let's say I want to have 20 particles, all spaced 10 pixels apart,
as in Figure 6.14.

![Figure 6.14: Twenty particles all spaced 10 pixels
apart](images/06_libraries/06_libraries_16.png)

I can loop from `i` equals `0` all the way up to `total`, creating new
particles and setting each one's `y` position to `i * 10`. The first
particle is at (0, 10), the second at (0, 20), the third at (0, 30), and
so on:

``` {.codesplit code-language="javascript"}
for (let i = 0; i < total; i++) {
  //{!1} Space them out along the x-axis.
  let particle = new Particle(i * length, 10, 4);
  //{!1} Add the particle to the physics world.
  physics.addParticle(particle);
  //{!1} Add the particle to the array.
  particles.push(particle);
}
```

Even though it's redundant, I'm adding the particles to both the
Toxiclibs.js `physics` world and the `particles` array. This will help
me manage the sketch (especially when I might have more than one string
of particles).

Now for the fun part: it's time to connect all the particles. Particle
index 0 will be connected to particle 1, particle 1 to particle 2, 2 to
3, 3 to 4, and so on (see Figure 6.15).

![Figure 6.15: Each particle is connected to the next particle in the
array.](images/06_libraries/06_libraries_17.png)

In other words, particle `i` needs to be connected to particle `i+1`
(except for when `i` represents the last element of the array):

``` {.codesplit code-language="javascript"}
// The loop stops before the last element (total – 1).
for (let i = 0; i < total - 1; i++) {
  // The spring connects particle i to i + 1.
  let spring = new VerletSpring2D(particles[i], particles[i + 1], spacing, 0.01);    
  //{!1} The spring must also be added to the world.
  physics.addSpring(spring);
}
```

Now, what if I want the string to hang from a fixed point? I can lock
one of the particles---perhaps the first, the last, or the middle one.
I'll go with the first:

``` {.codesplit code-language="javascript"}
particles[0].lock();
```

Finally, I need to draw the particles. Instead of drawing them as
circles, however, I want to treat them as points in a line. For that, I
can use `beginShape()`, `endShape()`, and `vertex()`, accessing the
individual particle positions from the array. I'll use the `show()`
method to draw only the last particle as a circle, creating a bob at the
end of the string.

::: {data-type="example"}
### Example 6.12: Soft Swinging Pendulum {#example-612-soft-swinging-pendulum}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/cIygo3QeX"
data-example-path="examples/06_libraries/6_12_soft_string">
<img src="examples/06_libraries/6_12_soft_string/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
function draw() {
  physics.update();

  background(255);

  stroke(0);
  noFill();
  beginShape();
  for (let particle of particles) {
    //{!1} Each particle represents one vertex in the string.
    vertex(particle.x, particle.y);
  }
  endShape();

  //{!1} This draws the last particle as a circle.
  particles[particles.length - 1].show();
}
```

The full code available on the book's website also demonstrates how to
drag the bob particle with the mouse.

::: {data-type="exercise"}
### Exercise 6.10 {#exercise-610}

Create a hanging cloth simulation using particles and springs. You'll
need to connect each particle with its vertical and horizontal
neighbors.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/x3FXo0wNZ"
data-example-path="examples/06_libraries/exercise_6_13_cloth_simulation">
<img
src="examples/06_libraries/exercise_6_13_cloth_simulation/screenshot.png" />
</div>
</figure>
:::

### A Soft-Body Character

Now that I've built a simple connected system---a single string of
particles---I'll expand on this idea to create a squishy, cute friend in
p5.js, otherwise known as a **soft-body character**. The first step is
to design a skeleton of connected particles. I'll begin with a very
simple design with only six vertices, as shown in Figure 6.16. Each
vertex (drawn as a dot) represents a `Particle` object, and each
connection (drawn as a line) represents a `Spring` object.

![Figure 6.16: A skeleton for a soft-body character. The vertices are
numbered according to their positions in an
array.](images/06_libraries/06_libraries_18.png)

Creating the particles is the easy part; it's exactly the same as
before! I'd like to make one change, though. Rather than having the
`setup()` function add the particles and springs to the physics world,
I'll incorporate this responsibility into the `Particle` constructor:

``` {.codesplit code-language="javascript"}
class Particle extends VerletParticle2D {
  constructor(x, y, r) {
    super(x, y);
    this.r = r;
    //{!1} Add the object to the global physics world. Inside a class, the object is referenced with this.
    physics.addParticle(this);
  }

  show() {
    fill(127);
    stroke(0);
    circle(this.x, this.y, this.r * 2);
  }
}
```

While it's not strictly necessary, I'd also like to make a `Spring`
class that inherits its functionality from `VerletSpring2D`. For this
example, I want the resting length of the spring to always be equal to
the distance between the skeleton's particles when they're first
created. Additionally, I'm keeping the implementation simple here by
hardcoding a uniform strength value of `0.01` in the `Spring`
constructor. You may want to enhance the example with a more
sophisticated design that sets varying degrees of springiness to the
different parts of the soft-body character.

``` {.codesplit code-language="javascript"}
class Spring extends VerletSpring2D {
  // The constructor receives two particles as arguments.
  constructor(a, b) {
    // Calculate the rest length as the distance between the particles.
    let length = dist(a.x, a.y, b.x, b.y);
    // Hardcode the spring strength.
    super(a, b, length, 0.01);
    //{!1} Another enhancement to have the object add itself to the physics world!    
    physics.addSpring(this);
  }
}
```

Now that I have the `Particle` and `Spring` classes, I can assemble the
character by adding a series of particles with hardcoded starting
positions to a `particles` array, and a series of spring connections to
a `springs` array.

``` {.codesplit code-language="javascript"}
//{!2} Store all the particles and springs in arrays.
let particles = [];
let springs = [];

function setup() {
  createCanvas(640, 240);
  physics = new VerletPhysics2D();
  // Create the vertex positions of the character as particles.
  particles.push(new Particle(200, 25));
  particles.push(new Particle(400, 25));
  particles.push(new Particle(350, 125));
  particles.push(new Particle(400, 225));
  particles.push(new Particle(200, 225));
  particles.push(new Particle(250, 125));
  // Connect the vertices with springs.
  springs.push(new Spring(particles[0], particles[1]));
  springs.push(new Spring(particles[1], particles[2]));
  springs.push(new Spring(particles[2], particles[3]));
  springs.push(new Spring(particles[3], particles[4]));
  springs.push(new Spring(particles[4], particles[5]));
  springs.push(new Spring(particles[5], particles[0]));
}
```

The beauty of this system is that you can easily expand it to create
your own design by adding more particles and springs! However, there's
one major issue here: I've made connections only around the perimeter of
the character. If I were to apply a force (like gravity) to the body, it
would instantly collapse onto itself. This is where additional internal
springs come into play, as shown in Figure 6.17. They keep the
character's structure stable while still allowing it to move and squish
in a realistic manner.

<figure>
<img src="images/06_libraries/06_libraries_19.png"
alt="Figure 6.17: Internal springs keep the structure from collapsing. This is just one possible design. Try others! " />
<figcaption>Figure 6.17: Internal springs keep the structure from
collapsing. This is just one possible design. Try others!</figcaption>
</figure>

The final example incorporates the additional springs from Figure 6.17,
a gravity force, and mouse interaction.

::: {data-type="example"}
### Example 6.13: Soft-Body Character {#example-613-soft-body-character}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/-1beeiwUK"
data-example-path="examples/06_libraries/soft_body_character_copy">
<img
src="examples/06_libraries/soft_body_character_copy/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
let physics;
let particles = [];
let springs = [];

function setup() {
  createCanvas(640, 240);
  physics = new VerletPhysics2D();
  physics.setWorldBounds(new Rect(0, 0, width, height));
  physics.addBehavior(new GravityBehavior(new Vec2D(0, 0.5)));
  // Particles at vertices of the character
  particles.push(new Particle(200, 25));
  particles.push(new Particle(400, 25));
  particles.push(new Particle(350, 125));
  particles.push(new Particle(400, 225));
  particles.push(new Particle(200, 225));
  particles.push(new Particle(250, 125));
  // Springs connecting vertices of the character
  springs.push(new Spring(particles[0], particles[1]));
  springs.push(new Spring(particles[1], particles[2]));
  springs.push(new Spring(particles[2], particles[3]));
  springs.push(new Spring(particles[3], particles[4]));
  springs.push(new Spring(particles[4], particles[5]));
  springs.push(new Spring(particles[5], particles[0]));
  //{!3} Three internal springs!
  springs.push(new Spring(particles[5], particles[2]));
  springs.push(new Spring(particles[0], particles[3]));
  springs.push(new Spring(particles[1], particles[4]));
}

function draw() {
  background(255);
  physics.update();
  //{!7} Draw the character as one shape.
  fill(127);
  stroke(0);
  beginShape();
  for (let particle of particles) {
    vertex(particle.x, particle.y);
  }
  endShape(CLOSE);
  //{!6} Mouse interaction
  if (mouseIsPressed) {
    particles[0].lock();
    particles[0].x = mouseX;
    particles[0].y = mouseY;
    particles[0].unlock();
  }
}
```

For the soft-body character example, you'll notice that I'm no longer
drawing all the elements of the physics simulation on the canvas! The
`show()` method of the particles isn't called, and the internal springs
that give the character its structure are not rendered with lines. In
fact, the springs themselves are never referenced after `setup()`, since
the character's shape is constructed from its particle positions. As
such, the springs array isn't strictly needed in this example, although
I do find it useful to have, considering it may be necessary for
enhancing the sketch in the future.

Considering the drawing as its own problem, distinct from the
character's skeletal structure, also opens up possibilities for adding
other design elements such as eyes or antennae. These creative
enhancements don't need to be directly connected to the physics of the
character, although they can be if you choose to do so!

::: {data-type="exercise"}
### Exercise 6.11 {#exercise-611}

Design your own soft-body character with additional vertices and
connections. What other design elements can you add? What other forces
and interactions can you incorporate?

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/hQw1Ih97c"
data-example-path="examples/06_libraries/exercise_6_11_soft_body_character_enhanced">
<img
src="examples/06_libraries/exercise_6_11_soft_body_character_enhanced/screenshot.png" />
</div>
</figure>
:::

### A Force-Directed Graph

Have you ever had the following thought? "I have a whole bunch of stuff
I want to draw, and I want all that stuff to be spaced out evenly in a
nice, neat, organized manner. Otherwise, I'll have trouble sleeping at
night."

This isn't an uncommon problem in computational design. One solution is
a **force-directed graph**, a visualization of elements---let's call
them *nodes*---whose positions aren't manually assigned. Instead, the
nodes arrange themselves according to a set of forces. While any forces
can be used, a classic approach uses spring forces: each node is
connected to every other node with a spring, such that when the springs
reach equilibrium, the nodes are evenly spaced (see Figure 6.18). Sounds
like a job for Toxiclibs.js!

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/0RqAtTXwI"
data-example-path="examples/06_libraries/figure_6_18">
<img src="examples/06_libraries/figure_6_18/screenshot.png" />
</div>
<figcaption>Figure 6.18: In this force-directed graph, clusters of
particles are connected by spring forces.</figcaption>
</figure>

To create a force-directed graph, I'll first need a class to describe an
individual node in the system. Because the term *node* is associated
with the JavaScript framework Node.js, I'll stick with the term
*particle* to avoid any confusion, and I'll continue using my `Particle`
class from the earlier soft-body examples.

Next, I'll encapsulate a list of *N* particles into a new class called
`Cluster` that represents the graph as a whole. The particles all start
out near the center of the canvas:

::: snip-below
``` {.codesplit code-language="javascript"}
class Cluster {
  // A cluster is initialized with N nodes.
  constructor(n, length) {
    this.particles = [];
    for (let i = 0; i < n; i++) {      
      // Here’s a funny little detail. The physics will misbehave
      // if all the particles are created in exactly the same position.
      let x = width / 2 + random(-1, 1);
      let y = height / 2 + random(-1, 1);
      this.particles.push(new Particle(x, y, 4));
    }
  }
```
:::

Let's assume that the `Cluster` class also has a `show()` method to draw
all the particles in the cluster and that I'll create a new `Cluster`
object in `setup()` and render it in `draw()`. If I ran the sketch as
is, nothing would happen. Why? Because I have yet to implement the whole
force-directed graph part! I need to connect every single node to every
other node with a spring. This is somewhat similar to creating a
soft-body character, but rather than handcraft a skeleton, I want to
write an algorithm to make all the connections automatically.

What exactly do I mean by that? Say I have five `Particle` objects: 0,
1, 2, 3, and 4. Figure 6.19 illustrates the connections.

![Figure 6.19: A network graph showing each of the five nodes connected
to every other node](images/06_libraries/06_libraries_20.png)

Notice two important details about the list of connections:

-   **No particle is connected to itself.** That is, 0 isn't connected
    to 0, 1 isn't connected to 1, and so on.
-   **Connections aren't repeated in reverse.** For example, if 0 is
    connected to 1, I don't need to explicitly say that 1 is also
    connected to 0. I already know this, based on the definition of how
    a spring works!

How do I write the code to make these connections for *N* particles?
Look at the four columns illustrated in Figure 6.19. They iterate all
the connections starting from particles 0 up to 3. This tells me that I
need to access each particle in the list from 0 to *N* -- 1:

::: {.snip-below .avoid-break}
``` {.codesplit code-language="javascript"}
    for (let i = 0; i < this.particles.length - 1; i++) {
      // Use the variable particle_i to store the particle reference.
      let particle_i = this.particles[i];
```
:::

Now look at the connections listed in Figure 6.19. I need to connect
node 0 to nodes 1, 2, and 3. For node 1, I connect 2 and 3. For node 2,
only 3. In general, for every node `i`, I need to iterate from `i + 1`
all the way until the end of the array. I'll use the counter variable
`j` for this purpose:

::: snip-below
``` {.codesplit code-language="javascript"}
      //{!1} Look at how j starts at i + 1.
      for (let j = i + 1; j < this.particles.length; j++) {
        let particle_j = this.particles[j];
```
:::

For every pair of particles `i` and `j`, I can then create a spring.
I'll go back to using `VerletSpring2D` directly, but you could also
incorporate a custom `Spring` class:

::: snip-above
``` {.codesplit code-language="javascript"}
        //{!1} The spring connects particles i and j.
        physics.addSpring(new VerletSpring2D(particle_i, particle_j, length, 0.01));
      }
    }
```
:::

Assuming those connections are made in the `Cluster` constructor, all
that's left is to create the cluster in `setup()` and call `show()` in
the `draw()` loop!

::: {data-type="example"}
### Example 6.14: Cluster {#example-614-cluster}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/_tbPaFqVX"
data-example-path="examples/06_libraries/6_13_force_directed_graph">
<img
src="examples/06_libraries/6_13_force_directed_graph/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
let { VerletPhysics2D, VerletParticle2D, VerletSpring2D } = toxi.physics2d;

let physics;
let cluster;

function setup() {
  createCanvas(640, 240);
  physics = new VerletPhysics2D();
  //{!1} Create a random cluster.
  cluster = new Cluster(floor(random(2, 20)), random(10, height / 2));
}

function draw() {
  physics.update();
  background(255);
  //{!1} Draw the cluster.
  cluster.show();
}
```

This example illustrates a force-directed graph but does not involve any
actual data! Here, the number of nodes in each cluster and the
equilibrium length between the nodes are assigned randomly, and the
spring strength has a constant value of `0.01`. In a real-world
application, these values could be determined based on your specific
data, hopefully resulting in a meaningful visualization of the
relationships within the data.

::: {data-type="exercise"}
### Exercise 6.12 {#exercise-612}

Design a cluster-like structure as a skeleton for a cute, cuddly,
squishy creature. Add gravity and mouse interaction.
:::

::: {data-type="exercise"}
### Exercise 6.13 {#exercise-613}

Expand the force-directed graph to have more than one `Cluster` object.
Use a `VerletMinDistanceSpring2D` object to connect cluster to cluster.
What kind of data might you visualize with this technique?

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/WexUSx7dN"
data-example-path="examples/06_libraries/exercise_6_13_force_directed_graph">
<img
src="examples/06_libraries/exercise_6_13_force_directed_graph/screenshot.png" />
</div>
</figure>
:::

## Attraction and Repulsion Behaviors

When it came time to create an attraction example for Matter.js, I
showed how the `Matter.Body`\
class includes an `applyForce()` method. All I then needed to do was
calculate the attraction\
force [F_g = (G \\times m_1 \\times m_2) \\div
d\^2]{data-type="equation"} as a vector and apply it to the body.
Similarly, the Toxiclibs.js `VerletParticle2D` class also includes a
method called `addForce()` that can apply any calculated\
force to a particle.

However, Toxiclibs.js takes this idea one step further by offering
built-in functionality for common forces (called behaviors) such as
attraction! For example, if you add an `AttractionBehavior` object to a
particular `VerletParticle2D` object, all other particles in the physics
world will experience an attraction force toward that particle.

Say I create an instance of my `Particle` class (which extends the
`VerletParticle2D` class):

``` {.codesplit code-language="javascript"}
let particle = new Particle(320, 120);
```

Now I can create an `AttractionBehavior` associated with that particle:

``` {.codesplit code-language="javascript"}
let distance = 20;
let strength = 0.1;
let behavior = new AttractionBehavior(particle, distance, strength);
```

Notice that the behavior is created with three arguments: a particle to
assign it to, a distance, and a strength. The distance specifies the
range within which the behavior will be applied. In this case, only
particles within 20 pixels will experience the attraction force. The
strength, of course, specifies how strong the force is.

Finally, in order for the force to be activated, the behavior needs to
be added to the physics world:

``` {.codesplit code-language="javascript"}
physics.addBehavior(behavior);
```

Now everything that lives in the physics simulation will always be
attracted to that particle, as long as it's within the distance
threshold.

::: avoid-break
The `AttractionBehavior` class is a very powerful tool. For example,
even though Toxiclibs.js doesn't automatically handle collisions like
Matter.js does, you can create a collision-like simulation by adding an
`AttractionBehavior` with a negative strength---a repulsive
behavior---to each and every particle. If the force is strong and
activated only within a short range (scaled to the particle's radius),
the result is much like a rigid-body collision. Here's how to modify the
`Particle` class to do this:

``` {.codesplit code-language="javascript"}
class Particle extends VerletParticle2D {
  constructor(x, y, r) {
    super(x, y);
    this.r = r;
    // Every time a Particle is made, an AttractionBehavior is
    // generated and added to the physics world.
    // Note that when the strength
    // is negative, it’s a repulsive force!
    physics.addBehavior(new AttractionBehavior(this, r * 4, -1));
  }

  show() {
    fill(127);
    stroke(0);
    circle(this.x, this.y, this.r * 2);
  }
}
```
:::

I can now remake the attraction example from [Chapter
2](/forces#section-forces) with a single `Attractor` object that exerts
an attraction behavior anywhere on the canvas. Even though the attractor
is centered, I'm using a distance threshold of the full `width` to
account for any movement of the attractor, and for particles located
outside the canvas boundaries.

::: {data-type="example"}
### Example 6.15: Attraction (and Repulsion) Behaviors {#example-615-attraction-and-repulsion-behaviors}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/tjIs8XaXP"
data-example-path="examples/06_libraries/6_14_attraction_behaviors">
<img
src="examples/06_libraries/6_14_attraction_behaviors/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Attractor extends VerletParticle2D {
  constructor(x, y, r) {
    super(x, y);
    this.r = r;
    // Attract all particles always.
    physics.addBehavior(new AttractionBehavior(this, width, 0.1));
    // Repel particles that come within its radius.
    physics.addBehavior(new AttractionBehavior(this, this.r, -10));
    // A nice improvement where the attractor adds itself to the physics
    physics.addParticle(this);
  }

  show() {
    fill(0);
    circle(this.x, this.y, this.r * 2);
  }
}
```

Just as discussed in ["Spatial
Subdivisions"](/autonomous-agents#spatial-subdivisions){.page-reference},
Toxiclibs.js projects with large numbers of particles interacting with
one another can run very slowly because of the
[N\^2]{data-type="equation"} nature of the algorithm (every particle
checking every other particle). To speed up the simulation, you could
use the manual `addForce()` method in conjunction with a binning
algorithm. Keep in mind, this would also require you to manually
calculate the attraction force, as the built-in `AttractionBehavior`
would no longer apply.

::: {data-type="exercise"}
### Exercise 6.14 {#exercise-614}

Use `AttractionBehavior` in conjunction with spring forces.
:::

::: {data-type="project"}
### The Ecosystem Project {#the-ecosystem-project-7}

Take your system of creatures from Chapter 5 and use a physics engine to
drive their motion and behaviors. Here are some possibilities:

-   Use Matter.js to allow collisions between creatures. Consider
    triggering an event when two creatures collide.
-   Use Matter.js to augment the design of your creatures. Build a
    skeleton with distance joints or make appendages with revolute
    joints.
-   Use Toxiclibs.js to augment the design of your creature. Use a chain
    of Toxiclibs.js particles for tentacles or a mesh of springs as a
    skeleton.
-   Use Toxiclibs.js to add attraction and repulsion behaviors to your
    creatures.
-   Use spring (or joint) connections between objects to control their
    interactions. Create and delete these springs on the fly. Consider
    making these connections visible or invisible to the viewer.

![image](images/06_libraries/06_libraries_21.png)
:::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
