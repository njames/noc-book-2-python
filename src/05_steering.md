# Steering
:::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::: {#section-autonomous-agents .section data-type="chapter"}
# Chapter 5. Autonomous Agents {#chapter-5-autonomous-agents}

::: chapter-opening-quote
> Life is a journey, not a destination.
>
> ::: chapter-opening-quote-source
> ---Ralph Waldo Emerson
> :::
:::

::: chapter-opening-figure
![image](images/05_steering/05_steering_1.png)

### Mo'i fish (photo courtesy of the US National Oceanic and Atmospheric Administration)

Six-finger threadfins (*Polydactylus sexfilis*), also known as fish of
kings, or *mo*'*i*, in Hawaiian, are shown swimming in a shoal. The mo'i
fish held a special status for Hawaiian royalty and were raised in
dedicated ponds to ensure their population growth and prevent their
extinction. The fish display a delicate and coordinated dance in their
collective movement, with each individual mo'i subtly influencing and
being influenced by its neighboring fish.
:::

So far, I've been demonstrating inanimate objects, lifeless shapes
sitting on the canvas that flop around when affected by forces in their
environment. But this is *The* ***Nature*** *of Code*. What if I could
breathe life into those shapes? What if those shapes could live by their
own rules? Can shapes have hopes and dreams and fears? These sorts of
questions are the domain of this chapter. They're what separate
unthinking objects from something much more interesting: autonomous
agents.

## Forces from Within

An **autonomous agent** is an entity that makes its own choices about
how to act in its environment, without any influence from a leader or
global plan. In this book, *acting* typically means moving. For example,
instead of simulating a particle that's passively drawn toward or
repelled by another shape because of a force like gravity, I'd now like
to design an entity that has the ability---or even the "desire"---to
make decisions about its movements. It could *decide* to move toward or
away from a target, like a moth drawn to a flame or a small fish evading
a predator.

The switch from inanimate objects to autonomous agents is a significant
conceptual leap, but the codebase will barely change. The desire for an
autonomous agent to move is just another force, like the force of
gravity or the force of the wind. It's just that now the force is coming
*from within*.

Here are three key components of autonomous agents to keep in mind as I
build this chapter's examples:

-   **An autonomous agent has a limited ability to perceive its
    environment.** It makes sense that a living, breathing being should
    be aware of its environment. However, this awareness doesn't refer
    to just the external environment but also to the agent's internal
    state---its position, velocity, and potentially other properties or
    even simulated emotions. Throughout the chapter, I'll explore ways
    agents can take their own state into account when making decisions.
    I'll also cover programming techniques for objects to store
    references to other objects and therefore "perceive" their
    surroundings. It's important to consider the word *limited* here.
    Are you designing an all-knowing circle that flies around a canvas,
    aware of everything else in that canvas? Or are you creating a shape
    that can examine other shapes only within 15 pixels of itself? Of
    course, there's no right answer to this question; it all depends on
    what you want. I'll explore several possibilities throughout this
    chapter, but in general, limitations are good for creating a
    simulation that feels more "natural." An insect, for example, may be
    aware of only the sights and smells that immediately surround it. To
    model a real-world creature, you could study the exact science of
    these limitations. Luckily, I can just make stuff up and try it out.

::: avoid-break
-   **An autonomous agent processes the information from its environment
    and calculates an action.** This will be the easy part, as the
    action is a force. The environment might tell the agent that there's
    a big, scary-looking shark swimming right at it, and the action will
    be a powerful force in the opposite direction.
:::

-   **An autonomous agent has no leader.** This third principle is
    something I care a little less about, depending on the context. For
    example, if you're designing a system for which it makes sense to
    have a leader barking commands at various entities, then that's what
    you'll want to implement. Nevertheless, many of the chapter's
    examples will have no leader for an important reason: toward the end
    of this chapter, I'll examine group behaviors and look at designing
    collections of autonomous agents that exhibit the properties of
    **complex systems**. These are intelligent and structured group
    dynamics that emerge not from a leader, but from the local
    interactions of the elements themselves.

I could start my exploration of autonomous agents in many places.
Artificial simulations of ant and termite colonies are fantastic
demonstrations of systems of agents, for example. For more on this
topic, I encourage you to read *Turtles, Termites, and Traffic Jams* by
Mitchel Resnick (Bradford Books, 1997). However, I want to begin by
examining agent behaviors that build on the work in the first four
chapters of this book: modeling motion with vectors and forces. And so
I'll return to the book's ever-changing hero class---once `Walker`, then
`Mover`, then `Particle`---and give it yet another incarnation.

## Vehicles and Steering

In the late 1980s, computer scientist [Craig
Reynolds](https://www.red3d.com/cwr/) developed algorithmic **steering**
behaviors for animated characters. These behaviors allowed individual
elements to navigate their digital environments in a lifelike manner,
with strategies for fleeing, wandering, arriving, pursuing, evading, and
more. Later, in his 1999 paper "Steering Behaviors for Autonomous
Characters," Reynolds uses the word *vehicle* to describe his autonomous
agents. I'll follow suit, calling my autonomous agent class `Vehicle`:

::: snip-below
``` {.codesplit code-language="javascript"}
class Vehicle {
  constructor() {
    this.position = createVector();
    this.velocity = createVector();
    this.acceleration = createVector();
  }

  /* What else do I need to add? */
```
:::

Like the `Mover` and `Particle` classes before it, the `Vehicle` class's
motion is controlled through its position, velocity, and acceleration
vectors. This will make the steering behaviors of a single autonomous
agent straightforward to implement. Yet by building a system of multiple
vehicles that steer themselves according to simple, locally based rules,
surprising levels of complexity emerge. The most famous example is
Reynolds's boids model for flocking or swarming behavior, which I'll
demonstrate in Example 5.11.

::: {data-type="note"}
### Why Vehicles?

In his book *Vehicles: Experiments in Synthetic Psychology* (Bradford
Books, 1986), Italian neuroscientist and cyberneticist Valentino
Braitenberg describes a series of hypothetical vehicles with simple
internal structures, writing, "This is an exercise in fictional science,
or science fiction, if you like that better." Braitenberg argues that
his extraordinarily simple mechanical vehicles manifest behaviors such
as fear, aggression, love, foresight, and optimism. Reynolds took his
inspiration from Braitenberg, and I'll take mine from Reynolds.
:::

Reynolds describes the motion of *idealized* vehicles---idealized
because he wasn't concerned with their actual engineering, but rather
started with the assumption that they work and respond to the rules
defined. These vehicles have three layers:

1.  **Action selection:** A vehicle has a goal (or goals) and can choose
    an action (or a combination of actions) based on that goal. This is
    essentially where I left off in the discussion of autonomous agents.
    The vehicle takes a look at its environment and *selects* an action
    based on a desire: "I see a zombie marching toward me. Since I don't
    want my brains to be eaten, I'm going to flee from the zombie." The
    goal is to keep one's brains, and the action is to flee. Reynolds's
    paper describes many goals and associated actions, such as seeking a
    target, avoiding an obstacle, and following a path. In a moment,
    I'll start building out these examples with p5.js code.
2.  **Steering:** Once an action has been selected, the vehicle has to
    calculate its next move. That next move will be a force---more
    specifically, a steering force. Luckily, Reynolds has developed a
    simple steering force formula that I'll use throughout the examples
    in this chapter: **steering force = desired velocity -- current
    velocity**. I'll get into the details of this formula and why it
    works so effectively in the next section.
3.  **Locomotion:** For the most part, I'm going to ignore this third
    layer. In the case of fleeing from zombies, the locomotion could be
    described as "left foot, right foot, left foot, right foot, as fast
    as you can." In a canvas, however, a rectangle, circle, or
    triangle's actual movement across a window is irrelevant, given that
    the motion is all an illusion in the first place. This isn't to say
    that you should ignore locomotion entirely, however. You'll find
    great value in thinking about the locomotive design of your vehicle
    and how you choose to animate it. The examples in this chapter will
    remain visually bare; a good exercise would be to elaborate on the
    animation style. For example, could you add spinning wheels,
    oscillating paddles, or shuffling legs?

Ultimately, the most important layer for you to consider is the first
one, action selection. What are the elements of your system, and what
are their goals? In this chapter, I'm going to cover a series of
steering behaviors (that is, actions): seeking, fleeing, following a
path, following a flow field, flocking with your neighbors, and so on.
As I've said in other chapters, however, the point isn't that you should
use these exact behaviors in all your projects. Rather, the point is to
show you *how* to model a steering behavior---*any* steering
behavior---in code, and to provide a foundation for designing and
developing your own vehicles with new and exciting goals and behaviors.

What's more, even though the examples in this chapter are highly literal
(follow that pixel!), you should allow yourself to think more abstractly
(like Braitenberg). What would it mean for your vehicle to have "love"
as its goal or "fear" as its driving force? Finally (and I'll address
this in ["Combining Behaviors"](#combining-behaviors){.page-reference}),
you won't get very far by developing simulations with only one action.
Yes, the first example's action will be to seek a target. But by being
creative---by making these steering behaviors *your own*---it will all
come down to mixing and matching multiple actions within the same
vehicle. View the coming examples not as singular behaviors to be
emulated, but as pieces of a larger puzzle that you'll eventually
assemble.

### The Steering Force

What exactly is a steering force? To answer, consider the following
scenario: a vehicle with a current velocity is seeking a target. For
fun, let's think of the vehicle as a bug-like creature that desires to
savor a delicious strawberry, as in Figure 5.1.

![Figure 5.1: A vehicle with a velocity and a
target](images/05_steering/05_steering_2.png)

The vehicle's goal and subsequent action is to seek the target. Thinking
back to [Chapter 2](/forces#section-forces), you might begin by making
the target an attractor and applying a gravitational force that pulls
the vehicle to\
the target. This would be a perfectly reasonable solution, but
conceptually it's not what I'm looking for here.

I don't want to simply calculate a force that pushes the vehicle toward
its target; rather, I want to ask the vehicle to make an intelligent
decision to steer toward the target based on its perception of its own
state (its speed and the direction in which it's currently moving) and
its environment (the location of the target). The vehicle should
consider how it desires to move (a vector pointing to the target),
compare that goal with how it's currently moving (its velocity), and
apply a force accordingly. That's exactly what Reynolds's steering force
formula says:

::: {data-type="equation"}
\\text{steering force} = \\text{desired velocity} - \\text{current
velocity}
:::

Or, as you might write in p5.js:

``` {.codesplit code-language="javascript"}
let steer = p5.Vector.sub(desired, velocity);
```

The *current* velocity isn't a problem: the `Vehicle` class already has
a variable for that. However, the *desired* velocity has to be
calculated. Take a look at Figure 5.2. If the vehicle's goal is defined
as *seeking the target*, then its desired velocity is a vector that
points from its current position to the target position.

![Figure 5.2: The vehicle's desired velocity points from its position to
the target. (The desired vector should point from the vehicle's center
to the target's center but is shortened for illustration
purposes.)](images/05_steering/05_steering_3.png)

Assuming a `p5.Vector` called `target` defining the target's position, I
then have this:

``` {.codesplit code-language="javascript"}
let desired = p5.Vector.sub(target, position);
```

There's more to the story, however. What if it is a high-resolution
canvas and the target is thousands of pixels away? Sure, the vehicle
might desire to teleport itself instantly to the target position with a
massive velocity, but this won't make for an effective animation. I'll
restate the desire as follows:

[*The vehicle desires to move toward the target* *at the maximum
possible speed.*]{.highlight}

In other words, the `desired` vector should point from the vehicle's
current position to the target position, with a magnitude equal to the
maximum speed of the vehicle, as shown in Figure 5.3.

<figure>
<img src="images/05_steering/05_steering_4.png"
alt="Figure 5.3: The magnitude of the vehicle’s desired velocity is max speed." />
<figcaption aria-hidden="true">Figure 5.3: The magnitude of the
vehicle’s desired velocity is <em>max speed</em>.</figcaption>
</figure>

The concept of maximum speed was introduced in [Chapter
1](/vectors#section-vectors) to ensure that a mover's speed remained
within a reasonable range. However, I didn't always use it in the
subsequent chapters. In Chapter 2, other forces such as friction and
drag kept the speed in check, while in [Chapter
3](/oscillation#section-oscillation), oscillation was caused by opposing
forces that kept the speed limited. In this chapter, maximum speed is a
key parameter for controlling the behavior of a steering agent, so I'll
include it in all the examples.

While I encourage you to consider how other forces such as friction and
drag could be combined with steering behaviors, I'm going to focus only
on steering forces for the time being. As such, I can include the
concept of maximum speed as a limiting factor in the force calculation.
First, I need to add a property to the `Vehicle` class setting the
maximum speed:

::: snip-below
``` {.codesplit code-language="javascript"}
class Vehicle {
  constructor() {
    this.position = createVector();
    this.velocity = createVector();
    this.acceleration = createVector();
    // Maximum speed
    this.maxspeed = ????;
  }
```
:::

Then, in the desired velocity calculation, I'll scale according to
maximum speed:

``` {.codesplit code-language="javascript"}
let desired = p5.Vector.sub(target, this.position);
desired.setMag(this.maxspeed);
```

Putting this all together, I can now write a method called `seek()` that
receives a `p5.Vector` target and calculates a steering force toward
that target:

``` {.codesplit code-language="javascript"}
  seek(target) {
    //{!1} Calculate the desired velocity to target at max speed.
    let desired = p5.Vector.sub(target,this.position);
    desired.setMag(this.maxspeed);

    // Reynolds’s formula for steering force
    let steer = p5.Vector.sub(desired, this.velocity);
    //{!1} Use the physics model and apply the force
    // to the object’s acceleration.
    this.applyForce(steer);
  }
```

Notice that I finish the method by passing the steering force into
`applyForce()`. This assumes that the code is built on top of the
foundation I developed in [Chapter 2](/forces#){.page-reference}.

To see why Reynolds's steering formula works so well, take a look at
Figure 5.4. It shows what the steering force looks like relative to the
vehicle and target positions.

![Figure 5.4: The vehicle applies a steering force equal to its desired
velocity minus its current
velocity.](images/05_steering/05_steering_5.png)

This force looks quite different from gravitational attraction. Remember
one of the principles of autonomous agents: an autonomous agent has a
*limited* ability to perceive its environment, including its own state.
Here's that ability, subtly but powerfully embedded into Reynolds's
steering formula. In the case of gravitational attraction, the force
pulling an object toward another is the same regardless of how that
object is moving. But here, the vehicle is actively aware of its own
velocity, and its steering force compensates accordingly. This adds a
lifelike quality to the simulation, as the way in which the vehicle
moves toward the target depends on its own understanding of its current
motion.

In all this excitement, I've missed one last step. What sort of vehicle
is this? Is it a super-sleek race car with amazing handling? Or a large
city bus that needs a lot of advance notice to turn? A graceful panda or
a lumbering elephant? The example code, as it stands, has no feature to
account for this variation in steering ability. For that, I need to
limit the magnitude of the steering force. I'll call this limit the
maximum force (or `maxforce` for short):

::: snip-below
``` {.codesplit code-language="javascript"}
class Vehicle {
  constructor() {
    this.position = createVector();
    this.velocity = createVector();
    this.acceleration = createVector();
    // Maximum speed
    this.maxspeed = ????;
    // Now I also have a maximum force.
    this.maxforce = ????;
  }
```
:::

Now I just need to impose that limit before applying the steering force:

::: {.snip-below .snip-above}
``` {.codesplit code-language="javascript"}
  seek(target) {
    let desired = p5.Vector.sub(target, this.position);
    desired.setMag(this.maxspeed);
    let steer = p5.Vector.sub(desired, this.velocity);
    //{!1} Limit the magnitude of the steering force.
    steer.limit(this.maxforce);
    this.applyForce(steer);
  }
```
:::

Limiting the steering force brings up an important point: the goal isn't
to get the vehicle to the target as fast as possible. If it were, I
would just say, "Set position equal to target," and the vehicle would
instantly teleport to that location! Instead, as Reynolds puts it, the
goal is to move the vehicle in a "lifelike and improvisational manner."

I'm trying to make the vehicle appear to be steering its way to the
target, so it's up to me to play with the forces and variables of the
system to simulate a given behavior. For example, a large maximum
steering force would result in a very different path than a small one
(see Figure 5.5). One isn't inherently better or worse than the other;
it depends on the desired effect. (And of course, these values need not
be fixed and could change based on other conditions. Perhaps a vehicle
has an *energy* property: the higher the energy, the better it can
steer.)

![Figure 5.5: The path for a stronger maximum force (left) versus a
weaker one (right)](images/05_steering/05_steering_6.png)

Here's the full `Vehicle` class, incorporating the rest of the elements
from the [Chapter 2](/forces#section-forces) `Mover` class.

::: {data-type="example"}
### Example 5.1: Seeking a Target {#example-51-seeking-a-target}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/Y74O77yxy"
data-example-path="examples/05_steering/noc_5_01_seek">
<img src="examples/05_steering/noc_5_01_seek/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Vehicle {
  constructor(x, y) {
    this.position = createVector(x, y);
    this.velocity = createVector(0, 0);
    this.acceleration = createVector(0, 0);
    //{!1} Additional variable for size
    this.r = 6.0;
    //{!2} Arbitrary values for max speed and force; try varying these!
    this.maxspeed = 8;
    this.maxforce = 0.2;
  }

  // Standard update function
  update() {
    this.velocity.add(this.acceleration);
    this.velocity.limit(this.maxspeed);
    this.position.add(this.velocity);
    this.acceleration.mult(0);
  }

  // Newton’s second law (skipping the math)
  applyForce(force) {
    this.acceleration.add(force);
  }

  // The seek steering force algorithm
  seek(target) {
    let desired = p5.Vector.sub(target, this.position);
    desired.setMag(this.maxspeed);
    let steer = p5.Vector.sub(desired, this.velocity);
    steer.limit(this.maxforce);
    this.applyForce(steer);
  }

  show() {
    // The vehicle is a triangle pointing in the direction of velocity.
    let angle = this.velocity.heading();
    fill(127);
    stroke(0);
    push();
    translate(this.position.x, this.position.y);
    rotate(angle);
    beginShape();
    vertex(this.r * 2, 0);
    vertex(-this.r * 2, -this.r);
    vertex(-this.r * 2, this.r);
    endShape(CLOSE);
    pop();
  }
}
```

Note that, unlike the circles used to represent movers and particles in
previous chapters, the `Vehicle` object is drawn as a triangle, defined
as three custom vertices set with `beginShape()` and `endShape()`. This
allows the vehicle to be represented in a way that indicates its
direction, determined using the `heading()` method, as demonstrated in
[Chapter 3](/oscillation#section-oscillation).

::: {data-type="exercise"}
### Exercise 5.1 {#exercise-51}

Implement a *fleeing* steering behavior (the desired velocity is the
same as *seek*, but pointed in the opposite direction).
:::

::: {data-type="exercise"}
### Exercise 5.2 {#exercise-52}

Create a sketch in which a vehicle's maximum force and maximum speed
don't remain constant but vary according to environmental factors.
:::

::: {data-type="exercise"}
### Exercise 5.3 {#exercise-53}

Implement a seeking behavior with a moving target, often referred to as
*pursuit*. In this case, your desired vector won't point toward the
object's current position, but rather its future position as
extrapolated from its current velocity. You'll see this ability for a
vehicle to "predict the future" in later examples. The solution is
covered in the ["Pursue & Evade" video on the Coding Train
website](https://thecodingtrain.com/pursuit).

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/RkDbZ0drr"
data-example-path="examples/05_steering/exercise_5_2">
<img src="examples/05_steering/exercise_5_2/screenshot.png" />
</div>
</figure>
:::

### The Arrive Behavior

After working for a bit with the seeking behavior, you're probably
asking yourself, "What if I want the vehicle to slow down as it
approaches the target?" Before I can even begin to answer this question,
I should explain why the seek behavior causes the vehicle to fly past
the target in the first place, forcing it to turn around and go back.
Consider the brain of a seeking vehicle. What is it thinking at each
frame of the animation?

-   I want to go as fast as possible toward the target.
-   I want to go as fast as possible toward the target.
-   I want to go as fast as possible toward the target.
-   I want to go as fast as possible toward the target.
-   I want to go as fast as possible toward the target.
-   and so on . . .

::: half-width-right
![Figure 5.6: The top vehicle has a desired velocity at maximum speed
and will overshoot the target. The bottom vehicle illustrates scaling
the desired velocity according to the distance from the target. (While I
encourage you to continue thinking about the vehicle as a cute, bug-like
creature, from this point it's drawn as a triangle to keep things
simple.)](images/05_steering/05_steering_7.png)
:::

The vehicle is so gosh darn excited about getting to the target that it
doesn't bother to make any intelligent decisions about its speed. No
matter the distance to the target, it always wants to go as fast as
possible. When the vehicle is very close, it will therefore end up
overshooting the target (see Figure 5.6, top).

In some cases, this is the desired behavior. (Consider a puppy going
after its favorite toy: it's not slowing down, no matter how close it
gets!) However, in many other cases (a car pulling into a parking spot,
a bee landing on a flower), the vehicle's thought process needs to
consider its speed relative to the distance from its target (see Figure
5.6, bottom). For example:

-   I'm very far away. I want to go as fast as possible toward the
    target.
-   I'm somewhat far away. I still want to go as fast as possible toward
    the target.
-   I'm getting close. I want to go more slowly toward the target.
-   I'm almost there. I want to go very slowly toward the target.
-   I'm there. I want to stop!

How can you implement this *arriving* behavior in code? Think back to
the `seek()` method. Which part of the code sets the magnitude of the
desired velocity?

``` {.codesplit code-language="javascript"}
   let desired = p5.Vector.sub(target, this.position);
   desired.setMag(this.maxspeed);
```

This always sets the magnitude of the `desired` vector to `maxspeed`, as
in Figure 5.7.

![Figure 5.7: The vehicles have a desired velocity with a magnitude set
to maximum speed, regardless of their relative distance to the
target.](images/05_steering/05_steering_8.png)

What if instead the desired velocity's magnitude were equal to half the
distance?

``` {.codesplit code-language="javascript"}
   let desired = p5.Vector.sub(target, this.position);
   desired.mult(0.5);
```

I'd still want to limit the magnitude of `desired` to no more than the
maximum speed, to keep vehicles that are very far away from going
ridiculously fast (Figure 5.8).

![Figure 5.8: The magnitude of each vehicle's desired velocity is equal
to half the distance to the target. In the case of the leftmost vehicle,
the velocity is constrained to the maximum
speed.](images/05_steering/05_steering_9.png)

While this change nicely demonstrates the goal of tying the desired
speed to the distance from the target, it's not a particularly good
solution. After all, 10 pixels away is rather close, and a desired speed
of 5 is rather large. Something like a desired velocity with a magnitude
equal to 5 percent of the distance might work better:

``` {.codesplit code-language="javascript"}
  let desired = p5.Vector.sub(target, this.position);
  desired.mult(0.05);
```

Reynolds describes an even more sophisticated approach. Imagine a circle
around the target with a given radius *r*. If the vehicle is within that
circle, it gradually slows down---from the maximum speed at the very
edge of the circle to zero speed at the target (Figure 5.9).

![Figure 5.9: Outside the circle, the magnitude of a vehicle's desired
velocity is set to the maximum speed. As vehicles enter the circle and
approach the target, their desired velocity magnitude
decreases.](images/05_steering/05_steering_10.png)

In other words, if the distance from the target is less than *r*, the
desired speed ranges from 0 to the maximum speed mapped according to
that distance.

::: {data-type="example"}
### Example 5.2: Arriving at a Target {#example-52-arriving-at-a-target}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/v-yJm8WUx"
data-example-path="examples/05_steering/noc_5_02_arrive">
<img src="examples/05_steering/noc_5_02_arrive/screenshot.png" />
</div>
</figure>
:::

::: avoid-break
``` {.codesplit code-language="javascript"}
  arrive(target) {
    let desired = p5.Vector.sub(target, this.position);
    //{!1} The distance is the magnitude of
    // the vector pointing from
    // the position to the target.
    let d = desired.mag();
    //{!1} If we are closer than 100 pixels . . .
    if (d < 100) {
      //{!2} . . . set the magnitude according to how close we are.
      let m = map(d, 0, 100, 0, this.maxspeed);
      desired.setMag(m);
    } else {
      //{!1} Otherwise, proceed at maximum speed.
      desired.setMag(this.maxspeed);
    }

    //{!1} The usual steering = desired – velocity
    let steer = p5.Vector.sub(desired, this.velocity);
    steer.limit(this.maxforce);
    this.applyForce(steer);
  }
```
:::

The arrive behavior is a great demonstration of an autonomous agent's
perception of the environment---including its own state. This model
differs from the inanimate forces of [Chapter
2](/forces#section-forces): a celestial body attracted to another body
doesn't know it is experiencing gravity, whereas a cheetah chasing its
prey knows it's chasing.

The key is in the way the forces are calculated. For instance, in the
gravitational attraction sketch (Example 2.6), the force always points
directly from the object to the target---the exact direction of the
desired velocity. Here, by contrast, the vehicle perceives its distance
to the target and adjusts its desired speed accordingly, slowing as it
gets closer. The force on the vehicle itself is therefore based not just
on the desired velocity but also on the desired velocity *relative to
its current velocity*. The vehicle accounts for its own state as part of
its assessment of the environment.

Put another way, the magic of Reynolds's *desired minus velocity*
equation is that it essentially makes the steering force a manifestation
of the current velocity's *error*: "I'm supposed to be going this fast
in this direction, but I'm actually going this fast in another
direction. My error is the difference between where I want to go and
where I'm currently going." Sometimes this can lead to seemingly
unexpected results, as in Figure 5.10.

![Figure 5.10: A vehicle moving toward its target faster than its
desired velocity will result in a steering force pointing away from the
target.](images/05_steering/05_steering_11.png)

In this example of the arrive behavior, the vehicle is moving too fast
toward the target. The steering force, or error, tells it to slow down
by actually pointing in the opposite direction, away from the target. By
contrast, with gravitational attraction, you would never have a force
pointing away from the target, no matter how close the target is. Taking
the error and applying it as a steering force results in more dynamic,
lifelike simulations.

### Your Own Behaviors

The first two examples I've covered---seek and arrive---boil down to
calculating a single vector for each behavior: the *desired* velocity.
In fact, every single one of Reynolds's steering behaviors follows this
same pattern. In this chapter, I'm going to walk through more of
Reynolds's behaviors---flow-field following, path following, and
flocking. First, however, I want to emphasize again that these are
*examples*---demonstrations of common steering behaviors that are useful
in procedural animation. They aren't the be-all and end-all of what
*you* can do. As long as you can come up with a vector that describes a
vehicle's *desired* velocity, you've created your own steering behavior.

For example, let's see how Reynolds defines the desired velocity for his
wandering behavior:

> *Wandering is a type of random steering which has some long-term
> order: the steering direction on one frame is related to the steering
> direction on the next frame. This produces more interesting motion
> than, for example, simply generating a random steering direction each
> frame.*

For Reynolds, the goal of wandering isn't random motion, but rather a
sense of moving in one direction for a little while, wandering off in
the next direction for a little bit, and so on. Figure 5.11 illustrates
how Reynolds calculates a target to seek in order to achieve such an
effect.

![Figure 5.11: The wandering steering behavior is calculated as seeking
a target that moves randomly along the perimeter of a circle projected
in front of the vehicle.](images/05_steering/05_steering_12.png)

First, the vehicle predicts its future position as a fixed distance in
front of it (in the direction of its current velocity). Then it draws a
circle with radius *r* centered on that position and picks a random
point along the circumference of the circle. That point, which moves
randomly around the circle for each frame of animation, is the vehicle's
target, so its desired velocity points in that direction.

Sounds absurd, right? Or, at the very least, a bit arbitrary. In fact,
this is a clever and thoughtful solution---it uses randomness to drive a
vehicle's steering, but constrains that randomness along the path of a
circle to keep the vehicle's movement from appearing jittery and, well,
totally random.

The seemingly random and arbitrary nature of this solution should drive
home the point I'm trying to make: these are *made-up* behaviors, even
if they're inspired by real-life motion. You can just as easily concoct
another elaborate scenario to compute a desired velocity. And you
should!

::: {data-type="exercise"}
### Exercise 5.4 {#exercise-54}

Write the code for Reynolds's wandering behavior. Use polar coordinates
to calculate the vehicle's target along a circular path.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/rgCpC9OV3"
data-example-path="examples/05_steering/noc_5_04_exercise_wander">
<img
src="examples/05_steering/noc_5_04_exercise_wander/screenshot.png" />
</div>
</figure>
:::

To give another example, say I want to create a steering behavior called
*stay within walls*. To define the desired velocity, I'll make a rule:
if a vehicle comes within a distance *d* of a wall, that vehicle desires
to move at maximum speed in the opposite direction of the wall (see
Figure 5.12).

![Figure 5.12: The desired velocity points away from the wall if the
vehicle gets too close.](images/05_steering/05_steering_13.png)

If I define the walls of the space as the edges of a canvas and an
`offset` distance equal to 25, I can write the code for this with a
series of `if` statements.

::: {data-type="example"}
### Example 5.3: "Stay Within Walls" Steering Behavior {#example-53-stay-within-walls-steering-behavior}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/fGNwVP3h7"
data-example-path="examples/05_steering/noc_5_03_stay_within_walls">
<img
src="examples/05_steering/noc_5_03_stay_within_walls/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
  // The method receives an offset from the edges.
  boundaries(offset) {
    // Start with a null desired velocity.
    let desired = null;
    //{.offset} Make a desired velocity that retains the y-direction of
    // the vehicle but points the x-direction directly away from
    // the canvas edges.
    if (this.position.x < offset) {
      desired = createVector(this.maxspeed, this.velocity.y);
    } else if (this.position.x > width - offset) {
      desired = createVector(-this.maxspeed, this.velocity.y);
    }
    if (this.position.y < offset) {
      desired = createVector(this.velocity.x, this.maxspeed);
    } else if (this.position.y > height - offset) {
      desired = createVector(this.velocity.x, -this.maxspeed);
    }
    // If the desired velocity is non-null, apply steering.
    if (desired !== null) {
      desired.normalize();
      desired.mult(this.maxspeed);
      let steer = p5.Vector.sub(desired, this.velocity);
      steer.limit(this.maxforce);
      this.applyForce(steer);
    }
  }
```

In this `boundaries()` method, you might be wondering why I set the
`desired` velocity to `null` at the outset. Why not just set `desired`
to a vector of 0? Remember, the steering force equals the desired
velocity minus the current velocity! If the vehicle desires to move at 0
velocity, the resulting force would slow the vehicle to a stop. By
initializing `desired` to `null` and checking that it's non-null before
applying the steering force, the vehicle won't be affected at all when
it's comfortably away from the edges of the canvas.

::: {data-type="exercise"}
### Exercise 5.5 {#exercise-55}

Come up with your own arbitrary scheme for calculating a desired
velocity.
:::

## Flow Fields

Another one of Reynolds's steering behaviors is **flow-field
following**. But what is a flow field? Think of the canvas as a grid
(Figure 5.13). In each cell of the grid lives an arrow pointing in a
certain direction---you know, a vector. As a vehicle moves around the
canvas, it asks, "Hey, what arrow is beneath me? That's my desired
velocity!"

![Figure 5.13: A 2D grid full of unit vectors pointing in random
directions](images/05_steering/05_steering_14.png)

Reynolds's own flow-field example involves the vehicle looking ahead to
its future position and following the vector at that spot. For
simplicity's sake, however, I'll instead have the vehicle follow the
vector at its current position.

Before I can write the additional code for the `Vehicle` class to follow
a flow field, I first need a class that describes the flow field. Since
a flow field is essentially a grid of vectors, a 2D array is a
convenient data structure to represent it, as I can reference each
element with two indices, the cell's column and row in the grid. If you
aren't familiar with 2D arrays, I suggest reviewing [my video tutorial
on "2D Arrays in JavaScript"](https://thecodingtrain.com/2d-array).

::: snip-below
``` {.codesplit code-language="javascript"}
class FlowField {
  constructor() {
    // Resolution of the grid relative to the canvas width and height in pixels
    this.resolution = ????;
    // How many columns and how many rows are in the grid?
    this.cols = ????;
    this.rows = ????;
    //{!4} The field will be a 2D array of vectors.
    this.field = new Array(this.cols);
    for (let i = 0; i < this.cols; i++) {
      this.field[i] = new Array(this.rows);
    }
  }
```
:::

How should I fill in the missing values? Let's say I have a canvas
that's 200 pixels wide by 200 pixels high. In theory, I could make a
flow field that has a vector for every single pixel, meaning 40,000
vectors total ([200 \\times 200]{data-type="equation"}). This isn't a
terribly unreasonable number, but in this context, one vector per pixel
is overkill. I can easily get by with, say, one vector every 10 pixels
([20 \\times 20 = 400]{data-type="equation"}). My `resolution` variable
sets the size of each cell in pixels. Then I can calculate the number of
columns and rows based on the size of the canvas divided by the
resolution:

``` {.codesplit code-language="javascript"}
  constructor() {
    this.resolution = 10;
    // The total number of columns equals the width divided by the resolution.
    this.cols = floor(width / this.resolution);
    //{!1} The total number of rows equals the height divided by the resolution.
    this.rows = floor(height / this.resolution);
    //{!4} The field will be a 2D array of vectors.
    this.field = new Array(this.cols);
    for (let i = 0; i < this.cols; i++) {
      this.field[i] = new Array(this.rows);
    }
  }
```

Now that I've set up the data structure for the flow field, it's time to
compute the flow field's vectors. How do I do that? However I want!
Perhaps I'd like every vector in the flow field pointing to the right
(Figure 5.14).

![Figure 5.14: A flow field with all vectors pointing to the
right](images/05_steering/05_steering_15.png)

For that, I can just set each vector to `(1, 0)`.

``` {.codesplit code-language="javascript"}
//{!2} Use a nested loop to hit every column
// and every row of the flow field.
for (let i = 0; i < this.cols; i++) {
  for (let j = 0; j < this.rows; j++) {
    //{!1} Arbitrary decision to make each vector point to the right
    this.field[i][j] = createVector(1, 0);
  }
}
```

Maybe I'd prefer the vectors to point in random directions (Figure
5.15).

![Figure 5.15: A flow field with vectors pointing in random
directions](images/05_steering/05_steering_16.png)

Easy. Just use the `p5.Vector` class's `random2D()` method to assign
each vector:

``` {.codesplit code-language="javascript"}
for (let i = 0; i < this.cols; i++) {
  for (let j = 0; j < this.rows; j++) {
    //{!1} A random vector
    this.field[i][j] = p5.Vector.random2D();
  }
}
```

::: avoid-break
What about using 2D Perlin noise (Figure 5.16)?

![Figure 5.16: A flow field calculated with Perlin
noise](images/05_steering/05_steering_17.png)
:::

Just map each noise value to an angle from [0]{data-type="equation"} to
[2\\pi]{data-type="equation"} and create a vector from that angle:

``` {.codesplit code-language="javascript"}
let xoff = 0;
for (let i = 0; i < this.cols; i++) {
  let yoff = 0;
  for (let j = 0; j < this.rows; j++) {
    //{!1 .offset-top} 2D noise
    let angle = map(noise(xoff, yoff), 0, 1, 0, TWO_PI);
    this.field[i][j] = p5.Vector.fromAngle(angle);
    yoff += 0.1;
  }
  xoff += 0.1;
}
```

Now I'm getting somewhere. Calculating the direction of the vectors by
using Perlin noise is a great way to simulate a variety of natural
effects, such as irregular gusts of wind or the meandering path of a
river. I'll note, however, that this noise mapping generates a field
that prefers flowing left. Since Perlin noise has a Gaussian-like
distribution, angles near [\\pi]{data-type="equation"} are more likely
to be selected. For Figure 5.16, I used a range of
[0]{data-type="equation"} to [4\\pi]{data-type="equation"} to counteract
this tendency, similarly to the way I applied
[4\\pi]{data-type="equation"} in [Chapter
4](/particles#section-particles) to represent a range of angles for
spinning confetti particles. Ultimately, of course, there's no one
correct way to calculate the vectors of a flow field; it's up to you to
decide what you're looking to simulate.

::: {data-type="exercise"}
### Exercise 5.6 {#exercise-56}

Write the code to calculate a flow field so that the vectors swirl in
circles around the center of the canvas. 

![image](images/05_steering/05_steering_18.png)

``` {.codesplit code-language="javascript"}
let x = i * width / cols;
let y = j * height / rows;
flowfield[i][j] = createVector(width / 2 - x, height / 2 - y);
flowfield[i][j].rotate(PI / 2);
```
:::

Now that I have a 2D array storing the flow-field vectors, I need a way
for the vehicle to look up its desired velocity. For that, I simply
divide the vehicle's x- and y-position by the resolution of the grid.
This gives me the indices of the desired vector in the 2D array. For
example, if the resolution is 10 and the vehicle is at (100, 50), I'll
want to look up column 10 and row 5:

``` {.codesplit code-language="javascript"}
let column = floor(this.position.x / this.resolution);
let row = floor(this.position.y / this.resolution);
```

Because a vehicle could theoretically wander off the p5.js canvas,
employing the `constrain()` function helps ensure that I don't look
outside the bounds of the flow-field array. Here's a method called
`lookup()`, which I'll add to the `FlowField` class, that receives a
vector (the position of the vehicle) and returns the corresponding
flow-field vector for that position:

``` {.codesplit code-language="javascript"}
  lookup(position) {
    //{!2 .offset-top} Use constrain().
    let column = constrain(floor(position.x / this.resolution), 0, this.cols - 1);
    let row = constrain(floor(position.y / this.resolution), 0, this.rows - 1);
    //{!1} Use copy() to ensure that a copy of the vector is returned.
    return this.field[column][row].copy();
  }
```

Before moving on to the `Vehicle` class, let's look at the `FlowField`
class code all together, this time using Perlin noise to compute the
vector directions:

``` {.codesplit code-language="javascript"}
class FlowField {
  constructor(r) {
    this.resolution = r;
    //{!2} Determine the number of columns and rows.
    this.cols = width / this.resolution;
    this.rows = height / this.resolution;
    //{!4} A flow field is a 2D array of vectors. The example includes a separate function to create that array.
    this.field = new Array(this.cols);
    for (let i = 0; i < this.cols; i++) {
      this.field[i] = new Array(this.rows);
    }
    this.init();
  }

  // The init() function fills the 2D array with vectors.
  init() {
    // Reseed noise for a new flow field each time.
    noiseSeed(random(10000));
    let xoff = 0;
    for (let i = 0; i < this.cols; i++) {
      let yoff = 0;
      for (let j = 0; j < this.rows; j++) {
        //{.code-wide} In this example, use Perlin noise to create the vectors.
        let angle = map(noise(xoff, yoff), 0, 1, 0, TWO_PI);
        this.field[i][j] = p5.Vector.fromAngle(angle);
        yoff += 0.1;
      }
      xoff += 0.1;
    }
  }

  //{.code-wide} A function to return a vector based on a position
  lookup(position) {
    let column = constrain(floor(position.x / this.resolution), 0, this.cols - 1);
    let row = constrain(floor(position.y / this.resolution), 0, this.rows - 1);
    return this.field[column][row].copy();
  }
}
```

Now let's assume there's a `FlowField` object called `flow`. Using that
object's `lookup()` method, a vehicle can then retrieve a desired
velocity from the flow field and use Reynolds's steering formula to
calculate a force.

::: {data-type="example"}
### Example 5.4: Flow-Field Following  {#example-54-flow-field-following}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/egribz8WV"
data-example-path="examples/05_steering/noc_5_04_flow_field">
<img src="examples/05_steering/noc_5_04_flow_field/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
  follow(flow) {
    // What is the vector at that spot in the flow field?
    let desired = flow.lookup(this.position);
    desired.setMag(this.maxspeed);
    //{!3} Steering is desired minus velocity.
    let steer = p5.Vector.sub(desired, this.velocity);
    steer.limit(this.maxforce);
    this.applyForce(steer);
  }
```

Notice that `lookup()` is a method of the `FlowField` class, rather than
of `Vehicle`. While you certainly could place `lookup()` within the
`Vehicle` class instead, from my perspective, placing it in `FlowField`
aligns best with the OOP principle of encapsulation. The lookup task,
which retrieves a vector based on a position from the flow field, is
inherently tied to the data of the `FlowField` object.

You may also notice some familiar elements from [Chapter
4](/particles#section-particles), such as the use of an array of
vehicles. Although the vehicles here operate independently, this is a
great first step toward thinking about the group behaviors that I'll
introduce later in this chapter.

::: {data-type="exercise"}
### Exercise 5.7 {#exercise-57}

Adapt the flow-field example so the vectors change over time. (Hint: Try
using the third dimension of Perlin noise!)
:::

::: {data-type="exercise"}
### Exercise 5.8 {#exercise-58}

Can you create a flow field from an image? For example, try having the
vectors point from dark to light colors (or vice versa).
:::

## Path Following

The next steering behavior formulated by Reynolds that I'd like to
explore is path following. But let me quickly clarify something first:
the behavior here is path *following*, not path *finding*. Pathfinding
refers to an algorithm that solves for the shortest distance between two
points, often in a maze. With **path following**, a predefined route, or
path, already exists, and the vehicle simply tries to follow it.

In this section, I will work through the algorithm, including the
corresponding mathematics and code. However, before doing so, it's
important to cover a key concept in vector math that I skipped over in
[Chapter 1](/vectors#section-vectors): **the dot product**. I haven't
needed it yet, but it's necessary here and likely will prove quite
useful for you beyond just this example.

### The Dot Product

Remember all the vector math covered in [Chapter
1](/vectors#section-vectors)? Add, subtract, multiply, and divide?
Figure 5.17 has a recap of some of these operations.

![Figure 5.17: Adding vectors and multiplying a vector by a
scalar](images/05_steering/05_steering_19.png)

Notice that multiplication involves multiplying a vector by a scalar
value. This makes sense; when you want a vector to be twice as large
(but facing the same direction), multiply it by 2. When you want it to
be half the size, multiply it by 0.5. However, several other
*multiplication-like* operations involve a pair of vectors that are
useful in certain scenarios---the dot product, the cross product, and
something called the Hadamard product. For now, I'm going to focus on
the dot product.

Assume vectors [\\vec{A}]{data-type="equation"} and
[\\vec{B}]{data-type="equation"}:

::: {data-type="equation"}
\\vec{A}=(a_x,a_y)
:::

::: {data-type="equation"}
\\vec{B}=(b_x,b_y)
:::

:::: avoid-break
The formula for the dot product (represented by the
[\\cdot]{data-type="equation"} character) is as follows:

::: {data-type="equation"}
\\vec{A}\\cdot\\vec{B}=(a_x\\times b_x) + (a_y\\times b_y)
:::
::::

Crucially, the result of the dot product is a scalar value (a single
number) and not a vector, even though the inputs are two vectors. For
example, say you have these two vectors:

::: {data-type="equation"}
\\vec{A}=(-3,5)
:::

::: {data-type="equation"}
\\vec{B}=(10,1)
:::

Their dot product is shown here:

::: {data-type="equation"}
\\vec{A}\\cdot\\vec{B} = (-3 \\times 10) + (5 \\times 1) = -30 + 5 = -25
:::

In p5.js, this translates to the following:

``` {.codesplit code-language="javascript"}
let a = createVector(-3, 5);
let b = createVector(10, 1);
// The p5.Vector class includes a function to calculate the dot product.
let n = a.dot(b);
```

If you look in the guts of the `p5.Vector` source code, you'll find a
pretty simple implementation of this `dot()` method:

``` {.codesplit code-language="javascript"}
dot(v) {
  //{!1} For 2D vectors, z is 0.
  return this.x * v.x + this.y * v.y + this.z * v.z;
}
```

This formula is simple enough, but why is the dot product necessary, and
when is it useful in coding? Well, one of the more common uses of the
dot product is to find the angle between two vectors. In fact, the dot
product can also be expressed as shown here:

::: {data-type="equation"}
\\vec{A}\\cdot\\vec{B} =
\|\|\\vec{A}\|\|\\times\|\|\\vec{B}\|\|\\times\\cos(\\theta)
:::

In other words, the dot product of [\\vec{A}]{data-type="equation"} and
[\\vec{B}]{data-type="equation"} is equal to the magnitude of
[\\vec{A}]{data-type="equation"} times the magnitude of
[\\vec{B}]{data-type="equation"} times the cosine of theta (with theta
being the angle between the two vectors [\\vec{A}]{data-type="equation"}
and [\\vec{B}]{data-type="equation"}).

The two dot-product formulas can be derived from each other with
[trigonometry](https://mathworld.wolfram.com/DotProduct.html), but I'm
happy not to follow that path and instead just operate on the following
assumption:

::: {data-type="equation"}
\|\|\\vec{A}\|\|\\times\|\|\\vec{B}\|\|\\times\\cos(\\theta)=(a_x\\times
b_x) + (a_y\\times b_y)
:::

::::: avoid-break
This works since both sides of the equation equal [\\vec{A} \\cdot
\\vec{B}]{data-type="equation"}. What does that assumption do for me?
Say I have two vectors [\\vec{A}]{data-type="equation"} and
[\\vec{B}]{data-type="equation"}:

::: {data-type="equation"}
\\vec{A}=(10,2)
:::

::: {data-type="equation"}
\\vec{B}=(4,-3)
:::
:::::

::: half-width-right
<figure>
<img src="images/05_steering/05_steering_20.png"
alt="Figure 5.18: The angle between two vectors \vec{A} and \vec{B}" />
<figcaption aria-hidden="true">Figure 5.18: The angle between two
vectors <span data-type="equation">\vec{A}</span> and <span
data-type="equation">\vec{B}</span></figcaption>
</figure>
:::

In this scenario, I know the components of the vectors but don't know
the angle [\\theta]{data-type="equation"} between them (see Figure
5.18). Using the dot-product formula, I can solve for the cosine of
[\\theta]{data-type="equation"}:

::: {data-type="equation"}
\\cos(\\theta)=\\frac{(a_x\\times b_x) + (a_y\\times
b_y)}{\|\|\\vec{A}\|\|\\times\|\|\\vec{B}\|\|}
:::

To solve for [\\theta]{data-type="equation"}, I can take the inverse
cosine, or arccosine (`acos` in p5.js), of the right side of the
equation:

::: {data-type="equation"}
\\theta=\\arccos\\left(\\frac{(a_x\\times b_x) + (a_y\\times
b_y)}{\|\|\\vec{A}\|\|\\times\|\|\\vec{B}\|\|}\\right)
:::

I'll do the math now with actual numbers:

::: {data-type="equation"}
\|\|\\vec{A}\|\|=10.2
:::

::: {data-type="equation"}
\|\|\\vec{B}\|\|=5
:::

::: {data-type="equation"}
\\theta=\\arccos\\left(\\frac{(10\\times4)+(2\\times-3)}{10.2\\times5}\\right)
:::

::: {data-type="equation"}
\\theta=\\arccos\\left(\\frac{34}{51}\\right)
:::

::: {data-type="equation"}
\\theta \\approx48\^\\circ
:::

Here's the p5.js version:

``` {.codesplit code-language="javascript"}
let a = createVector(10, 2);
let b = createVector(4, -3);
let angle = acos(a.dot(b) / (a.mag() * b.mag()));
```

Turns out, if you again dig into the guts of the p5.js source code,
you'll find a method called `angleBetween` that implements this exact
algorithm.

::: avoid-break
``` {.codesplit code-language="javascript"}
  angleBetween(v) {
    let dot = this.dot(v);
    let angle = Math.acos(dot / (this.mag() * v.mag()));
    return angle;
  }
```
:::

Sure, I could have told you about this `angleBetween()` method to begin
with, but understanding the dot product in detail will better prepare
you for the upcoming path-following examples and help you see how the
dot product fits into a concept called *scalar projection*.

::: {data-type="exercise"}
### Exercise 5.9 {#exercise-59}

Create a sketch that shows the angle between two vectors.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/G80pBHVOE"
data-example-path="examples/05_steering/exercise_5_9_angle_between">
<img
src="examples/05_steering/exercise_5_9_angle_between/screenshot.png" />
</div>
</figure>
:::

There are a couple of things to note about dot products:

-   If two vectors ([\\vec{A}]{data-type="equation"} and
    [\\vec{B}]{data-type="equation"}) are orthogonal (that is,
    perpendicular), their dot product
    ([\\vec{A}\\cdot\\vec{B}]{data-type="equation"}) is equal to 0.
-   If two vectors are unit vectors, their dot product is equal to the
    cosine of the angle between them. In other words,
    [\\vec{A}\\cdot\\vec{B}=\\cos(\\theta)]{data-type="equation"} if
    [\\vec{A}]{data-type="equation"} and
    [\\vec{B}]{data-type="equation"} are of length 1.

Now that I've covered the fundamentals of the dot product, I can return
to Reynolds's path-following algorithm.

### Simple Path Following

Figure 5.19 depicts all the ingredients of the path-following behavior.
A lot of components are at play here beyond just a vehicle and target,
so take some time to review the full diagram. I'll then slowly unpack
the algorithm piece by piece.

<figure>
<img src="images/05_steering/05_steering_21.png"
alt="Figure 5.19: Path following requires a path, a vehicle, a future position, a normal to the path, and a target." />
<figcaption aria-hidden="true">Figure 5.19: Path following requires a
path, a vehicle, a future position, a <em>normal</em> to the path, and a
target.</figcaption>
</figure>

First, what do I mean by a *path*? Many techniques can be used to
implement a path, but one simple way is to define a path as a series of
connected points, as in Figure 5.20.

![Figure 5.20: A path is a sequence of connected
points. ](images/05_steering/05_steering_22.png)

The simplest version of this path would be a line between two points
(Figure 5.21).

![Figure 5.21: A path with a start, end, and
radius](images/05_steering/05_steering_23.png)

I'm also going to consider a path to have a *radius*. If the path is a
road, the radius is the road's width. With a smaller radius, vehicles
have to follow the path more closely; a wider radius allows them to
stray a bit more to either side of the path.

Now I'll put this into a class.

::: {data-type="example"}
### Example 5.5: Creating a Path Object {#example-55-creating-a-path-object}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/8SALyBTym"
data-example-path="examples/05_steering/nature_of_code_example_5_5_path_only">
<img
src="examples/05_steering/nature_of_code_example_5_5_path_only/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Path {
  constructor() {
    // A path has a radius, indicating its width.
    this.radius = 20;
    //{!2} A path has only two points, start and end.
    this.start = createVector(0, height / 3);
    this.end = createVector(width, (2 * height) / 3);
  }

  //{!1} Display the path.
  show() {
    strokeWeight(this.radius * 2);
    stroke(0, 100);
    line(this.start.x, this.start.y, this.end.x, this.end.y);
    strokeWeight(1);
    stroke(0);
    line(this.start.x, this.start.y, this.end.x, this.end.y);
  }
}
```

Now, assume that a vehicle is outside the path's radius, moving with a
velocity, as in Figure 5.22.

![Figure 5.22: Adding a vehicle moving off and away from the
path](images/05_steering/05_steering_24.png)

The first step is to predict (assuming a constant velocity) where that
vehicle will be in the future:

``` {.codesplit code-language="javascript"}
// Start by making a copy of the velocity.
let future = vel.copy();
// Look 25 pixels ahead by setting the magnitude.
future.setMag(25);
// Add the vector to the position to find the future position.
future.add(this.position);
```

Once I have that position, it's time to determine the distance from that
predicted position to the path. If it's very far away, the vehicle has
strayed from the path and needs to steer back toward it. If the vehicle
is on the path, all is well and the vehicle can continue on its way.

Essentially, I need to calculate the distance between a point (the
future position) and a line (the path). That distance is defined as the
length of the **normal**, a vector that extends from the point to the
line and is perpendicular to the line (Figure 5.23).

![Figure 5.23: The normal is a vector that extends from the future
position to the path and is perpendicular to the
path.](images/05_steering/05_steering_25.png)

How do I find the normal? First, I can define a vector (call it
[\\vec{A}]{data-type="equation"}) that extends from the path's starting
point to the vehicle's future position:

``` {.codesplit code-language="javascript"}
let a = p5.Vector.sub(future, path.start);
```

Next, I can define a vector (call it [\\vec{B}]{data-type="equation"})
that points from the start of the path to the end:

``` {.codesplit code-language="javascript"}
let b = p5.Vector.sub(path.end, path.start);
```

Now, with a little trigonometry (the *cah* in *sohcahtoa*), I can
calculate the distance from the path's start to the normal point. As
shown in Figure 5.24, it's [\|\|\\vec{A}\|\| \\times
\\cos(\\theta)]{data-type="equation"}.

<figure>
<img src="images/05_steering/05_steering_26.png"
alt="Figure 5.24: The distance from the start of the path to the normal is ||\vec{A}|| \times \cos(\theta)." />
<figcaption aria-hidden="true">Figure 5.24: The distance from the start
of the path to the normal is <span data-type="equation">||\vec{A}||
\times \cos(\theta)</span>.</figcaption>
</figure>

If I only knew [\\theta]{data-type="equation"}, I could find that normal
point with the code shown next.

``` {.codesplit code-language="javascript"}
//{!1} Get the distance from the start to the normal.
let d = a.mag() * cos(theta);
// Scale vector b to that distance.
b.setMag(d);
// The normal point can be found by adding the scaled version of b to the path’s starting point.
let normalPoint = p5.Vector.add(path.start, b);
```

Luckily, if the dot product has taught me anything, it's that given two
vectors, I can calculate the angle between those vectors!

``` {.codesplit code-language="javascript"}
//{!1} What is theta? The angle between A and B!
let theta = p5.Vector.angleBetween(a, b);
let d = a.mag() * cos(theta);
b.setMag(d);
let normalPoint = p5.Vector.add(path.start, b);
```

While this code will work, I can make one more simplification. Looking
again, you'll see that the magnitude for vector
[\\vec{B}]{data-type="equation"} is set to `a.mag() * cos(theta)`, which
is the code translation of the following:

::: {data-type="equation"}
\|\|\\vec{A}\|\|\\times\\cos(\\theta)
:::

And, recall this:

::: {data-type="equation"}
\\vec{A}\\cdot\\vec{B}=\|\|\\vec{A}\|\|\\times\|\|\\vec{B}\|\|\\times\\cos(\\theta)
:::

Now, what if [\\vec{B}]{data-type="equation"} is a unit vector of length
1? Then you have this:

::: {data-type="equation"}
\\vec{A}\\cdot\\vec{B}=\|\|\\vec{A}\|\|\\times1\\times\\cos(\\theta)
:::

Or, more simply:

::: {data-type="equation"}
\\vec{A}\\cdot\\vec{B}=\|\|\\vec{A}\|\|\\times\\cos(\\theta)
:::

When [\\vec{B}]{data-type="equation"} is a unit vector,
[\|\|\\vec{A}\|\| \\times \\cos(\\theta)]{data-type="equation"} is the
same as the dot product of [\\vec{A}]{data-type="equation"} and
[\\vec{B}]{data-type="equation"}. Turning `b` into a unit vector is as
simple as calling `normalize()`. I can therefore bypass calculating
`theta` with `angleBetween()` and simplify the code as follows:

``` {.codesplit code-language="javascript"}
let theta = p5.Vector.angleBetween(a, b);
let d = a.mag() * cos(theta);
b.setMag(d);
//{!2} Normalize b and use the dot product to set b’s length.
b.normalize();
b.setMag(a.dot(b));
let normalPoint = p5.Vector.add(path.start, b);
```

This process of scaling [\\vec{B}]{data-type="equation"} according to
the normal point is commonly known as **scalar projection**. We say that
[\|\|\\vec{A}\|\|\\times\\cos(\\theta)]{data-type="equation"} **** *is
the scalar projection of* [\\vec{A}]{data-type="equation"} *onto*
[\\vec{B}]{data-type="equation"}, as in Figure 5.25.

<figure>
<img src="images/05_steering/05_steering_27.png"
alt="Figure 5.25: The scalar projection of \vec{A} onto \vec{B} is equal to ||\vec{A}||\times\cos(\theta)." />
<figcaption aria-hidden="true">Figure 5.25: The scalar projection of
<span data-type="equation">\vec{A}</span> onto <span
data-type="equation">\vec{B}</span> is equal to <span
data-type="equation">||\vec{A}||\times\cos(\theta)</span>.</figcaption>
</figure>

Once I have the normal point along the path, the next step is to decide
whether and how the vehicle should steer toward the path. Reynolds's
algorithm states that the vehicle should steer toward the path only if
it's in danger of straying beyond the path---that is, if the distance
between the normal point and the predicted future position is greater
than the path's radius. This is illustrated in Figure 5.26.

![Figure 5.26: A vehicle with a future position on the path (top) and
one that's outside the path
(bottom)](images/05_steering/05_steering_28.png)

I can encode that logic with a simple `if` statement and use my earlier
`seek()` method to steer the vehicle when necessary.

::: avoid-break
``` {.codesplit code-language="javascript"}
let distance = p5.Vector.dist(future, normalPoint);
// If the vehicle is outside the path, seek the target.
if (distance > path.radius) {
  //{!1} The desired velocity and steering force can use the seek() method created in Example 5.1.
  this.seek(target);
}
```
:::

But what's the target that the path follower is seeking? Reynolds's
algorithm involves picking a point ahead of the normal on the path.
Since I know the vector that defines the path
([\\vec{B}]{data-type="equation"}), I can implement this point ahead by
adding a vector that points in [\\vec{B}]{data-type="equation"}'s
direction to the vector representing the normal point, as in Figure
5.27.

![Figure 5.27: The target is 25 pixels (an arbitrary choice) ahead of
the normal point along the path.](images/05_steering/05_steering_29.png)

I'll arbitrarily say the target should be 25 pixels ahead of the normal:

``` {.codesplit code-language="javascript"}
let distance = p5.Vector.dist(future, normalPoint);
if (distance > path.radius) {
  //{!2} Set the magnitude to 25 pixels (picked arbitrarily).
  b.setMag(25);
  //{!1} Add b to normalPoint to find the target 25 pixels ahead on the path.
  let target = p5.Vector.add(normalPoint, b);
  //{!1} Seek the target.
  this.seek(target);
}
```

Putting it all together, here's the path-following method in the
`Vehicle` class.

::: {data-type="example"}
### Example 5.6: Simple Path Following {#example-56-simple-path-following}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/zcH21K3T3"
data-example-path="examples/05_steering/noc_5_05_path_following_simple">
<img
src="examples/05_steering/noc_5_05_path_following_simple/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
  follow(path) {
    //{!3} Step 1: Predict the vehicle’s future position.
    let future = this.velocity.copy();
    future.setMag(25);
    future.add(this.position);
    //{!1} Step 2: Find the normal point along the path.
    let normalPoint = getNormalPoint(future, path.start, path.end);
    //{!3} Step 3: Look a little farther along the path and set a target.
    // (To optimize, this could be moved into the if statement to find the target only if it’s off the path.)
    let b = p5.Vector.sub(path.end, path.start);
    b.setMag(25);
    let target = p5.Vector.add(normalPoint, b);
    //{!3} Step 4: If you’re off the path,
    // seek that target in order to get on the path.
    let distance = p5.Vector.dist(normalPoint, future);
    if (distance > path.radius) {
      this.seek(target);
    }
  }
```

::: half-width-right
<figure>
<img src="images/05_steering/05_steering_30.png"
alt="Figure 5.28: The elements of the getNormalPoint() function: position, a, and b" />
<figcaption aria-hidden="true">Figure 5.28: The elements of the
<code>getNormalPoint()</code> function: <code>position</code>,
<code>a</code>, and <code>b</code></figcaption>
</figure>
:::

Notice that instead of using all that dot-product and scalar projection
code to find the normal point, I call the `getNormalPoint()` function.
In cases like this, it's useful to break out the code that performs a
specific task (finding a normal point) into a function that can be
called when required. The function takes three vector arguments (see
Figure 5.28): the first defines a point *p* in Cartesian space (the
vehicle's future position), and the second and third define a line
segment between two points *a* and *b* (the path).

``` {.codesplit code-language="javascript"}
  getNormalPoint(position, a, b) {
    // Vector that points from a to position
    let vectorA = p5.Vector.sub(position, a);
    // Vector that points from a to b
    let vectorB = p5.Vector.sub(b, a);
    // Use the dot product for scalar projection.
    vectorB.normalize();
    vectorB.mult(vectorA.dot(vectorB));
    //{!1} Find the normal point along the line segment.
    let normalPoint = p5.Vector.add(a, vectorB);
    return normalPoint;
  }
```

What do I have so far? I have a `Path` class that defines a path as a
line between two points. I have a `Vehicle` class with a method to
follow the path (using steering to seek a target along the path). In
all, this makes for a decent example, and yet it's pretty darn limiting.
What's missing?

Take a deep breath. You're almost there.

### Path Following with Multiple Segments

What if I want a vehicle to follow a more complex path than just a
single straight line? Perhaps a curved path that moves in a variety of
directions, as in Figure 5.29?

![Figure 5.29: A more complex
path](images/05_steering/05_steering_31.png)

Maybe I'm being a little too ambitious. I *could* investigate algorithms
for following a curved path, but I'm much less likely to end up needing
a cool compress on my forehead if I stick with straight line segments,
like those in Figure 5.30. I could always still *draw* the path as a
curve, but it's best to approximate it behind the scenes with simplified
geometric forms for the necessary calculations.

![Figure 5.30: The same curved path, but approximated as connected line
segments](images/05_steering/05_steering_32.png)

If I made path following work with one line segment, how do I make it
work with a series of connected line segments? The key is in the way I
find the target point along the path.

To find the target with just one line segment, I had to compute the
normal to that line segment. Now that I have a series of line segments,
I also have a series of normal points to be computed---one for each
segment (see Figure 5.31). Which one does the vehicle choose? The
solution Reynolds proposed is to pick the normal point that is (a)
closest and (b) on the path.

![Figure 5.31: Finding the closest normal point along a series of
connected line segments](images/05_steering/05_steering_33.png)

If you have a point and an infinitely long line, you'll always have a
normal point that touches the line. But if you have a point and a finite
line segment, you won't necessarily find a normal that's on the line
segment. If this happens for any of the segments, I can disqualify those
normals. Once I'm left with just those normals that are on the path
(only two in Figure 5.31), I pick the one that's shortest.

To write the code for this, I'll expand the `Path` class to have an
array of points (rather than just the start and end).

::: {data-type="example"}
### Example 5.7: Path Made of Multiple Line Segments {#example-57-path-made-of-multiple-line-segments}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/ntGaosFQ7"
data-example-path="examples/05_steering/5_6_path_segments_only">
<img src="examples/05_steering/5_6_path_segments_only/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
class Path {
  constructor() {
    this.radius = 20;
    //{!1} A path is now an array of points (p5.Vector objects).
    this.points = [];
  }

  //{!4} This method allows you to add points to the path.
  addPoint(x, y) {
    let pathPoint = createVector(x, y);
    this.points.push(pathPoint);
  }

  show() {
    //{!8} Draw a thicker gray line for the path radius.
    stroke(200);
    strokeWeight(this.radius * 2);
    noFill();
    beginShape();
    for (let pathPoint of this.points) {
      vertex(pathPoint.x, pathPoint.y);
    }
    endShape();
    //{!7} Draw a thin line for the path center.
    stroke(0);
    strokeWeight(1);
    beginShape();
    for (let pathPoint of this.points) {
      vertex(pathPoint.x, pathPoint.y);
    }
    endShape();
  }
}
```

Now that the `Path` class has been updated, it's the vehicle's turn to
learn how to accommodate multiple line segments. All it did before was
find the normal for one line. Using a loop, it can find the normals for
all the segments:

::: snip-below
``` {.codesplit code-language="javascript"}
for (let i = 0; i < path.points.length - 1; i++) {
  let a = path.points[i];
  let b = path.points[i + 1];
  //{!1 .offset-top} Find the normal for each line segment.
  let normalPoint = getNormalPoint(future, a, b);
```
:::

The next step is to test whether the normal point is actually between
points `a` and `b`. Since I know the path goes from left to right in
this example, I can test whether the `x` component of `normalPoint` is
outside the `x` components of `a` and `b`.

::: {.snip-below .snip-above .avoid-break}
``` {.codesplit code-language="javascript"}
   if (normalPoint.x < a.x || normalPoint.x > b.x) {
     //{!1} Use the endpoint of the segment
     // as your normal point if you can’t find one.
     normalPoint = b.copy();
   }
```
:::

If the normal point is not within the line segment, I'll just pretend
the end point of that line segment is the normal. (You might also try
the beginning point, depending on the particulars of your path.) This
will ensure that the vehicle always stays on the path, even if it strays
beyond the bounds of the line segments.

::: {data-type="exercise"}
### Exercise 5.10 {#exercise-510}

A more general-purpose way to test whether the normal point lies on the
segment is to sum the distances between `normalPoint` and `a` and `b`.
If the result is greater than the length of the line segment, the normal
is outside the segment. Can you write this algorithm with p5.js?
:::

Finally, I need to find the closest normal point to the vehicle. To
accomplish this, I can start with a very high "world record" distance
and iterate through each normal point to see if it beats (is less than)
the record. Each time a normal point beats the record, the world record
is updated, and the winning point is stored in a variable named
`target`. At the end of the loop, `target` will hold the closest normal
point.

::: {data-type="example"}
### Example 5.8: Path Following {#example-58-path-following}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/1T-6LupvJ"
data-example-path="examples/05_steering/noc_5_08_path_following">
<img
src="examples/05_steering/noc_5_08_path_following/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
let target = null;
//{!1} Start with a very high record that can easily be beaten, like infinity!
let worldRecord = Infinity;
for (let i = 0; i < path.points.length - 1; i++) {
  let a = path.points[i];
  let b = path.points[i + 1];
  let normalPoint = getNormalPoint(future, a, b);
  if (normalPoint.x < a.x || normalPoint.x > b.x) {
    normalPoint = b.copy();
  }

  let distance = p5.Vector.dist(future, normalPoint);
  //{!4} If you beat the record, this should be your target.
  if (distance < worldRecord) {
    worldRecord = distance;
    target = normalPoint.copy();
  }
  //{!3} Look at the direction of the line segment in order to seek a little bit ahead of the normal.
  let dir = p5.Vector.sub(b, a);
  dir.setMag(25);
  target.add(dir);
}
```

You may have noticed the use of `Infinity` to initialize `worldRecord`.
In JavaScript, `Infinity` is a special numeric value that represents,
well, infinity. It works in this case because I need a starting value
that will always be higher than any plausible distance calculated in the
code. The first calculated distance will always set a new world record,
against which all the others will be compared.

I also want to highlight the hardcoded value of `25`, which sets the
distance ahead on the path from the normal for the target. Reynolds
indicates that this value should be dynamic and calculated based on the
vehicle's distance to the path and its speed. Give this a try and see
how it improves the accuracy or responsiveness of the path-following
behavior!

::: {data-type="exercise"}
### Exercise 5.11 {#exercise-511}

Create a path that changes over time. Can the points that define the
path have their own steering behaviors?
:::

## Complex Systems

I said the purpose of this chapter is to breathe life into the things
that move around p5.js canvases. You've come a long way by learning to
write the code for an autonomous agent and playing with examples of that
agent's individual behaviors. But this is no place to stop. Yes, a
vehicle is a simulated being that makes decisions about how to seek and
flow and follow. But what is a life led alone, without the love and
support of others?

And so, as a logical next step, I'll take the work I've done developing
behaviors for individual autonomous agents and apply it to simulations
that involve *many* autonomous agents operating in parallel---agents
that have an ability to perceive not only their physical environment but
also the actions of their fellow agents, and then act accordingly. In
other words, I want to create complex systems with p5.js.

A **complex system** is typically defined as a system that's more than
the sum of its parts. While the individual elements of the system may be
incredibly simple and easily understood, the behavior of the system as a
whole can be highly complex, intelligent, and difficult to predict.

Think, for example, about a tiny, crawling ant---one single ant. An ant
is an autonomous agent; it can perceive its environment (using antennae
to gather information about the direction and strength of chemical
signals) and make decisions about how to move based on those signals.
But can a single ant acting alone build a nest, gather food, or defend
its queen? An ant is a simple unit that can perceive only its immediate
environment. A *colony* of ants, however, is a sophisticated, complex
system, a superorganism of components that work together to accomplish
difficult, complicated goals.

Here are three key principles that will guide my work with complex
systems:

-   **Simple units have short-range relationships.** This is what I've
    been building all along: vehicles that have a limited perception of
    their environment.
-   **Simple units operate in parallel.** For every cycle through the
    `draw()` loop, each unit will calculate its own steering forces.
    This will create the appearance of all the units working in
    parallel.
-   **Systems as a whole exhibit emergent phenomena.** Complex
    behaviors, patterns, and intelligence can emerge from the
    interactions among simple units. This phenomenon occurs in nature,
    such as in ant colonies, migration patterns, earthquakes, and
    snowflakes. The question is whether the same results can be achieved
    in a p5.js sketch.

Beyond these core principles, three additional qualities of complex
systems will help frame the discussion, as well as provide guidelines
for features to include in a software simulation. It's important to
acknowledge that this is a fuzzy set of characteristics, and not all
complex systems have all\
of them:

-   **Nonlinearity:** This aspect of complex systems is often casually
    referred to as the *butterfly effect*, coined by mathematician and
    meteorologist Edward Norton Lorenz, a pioneer in the study of chaos
    theory. In 1961, Lorenz was running a computer weather simulation
    for the second time and, perhaps to save a little time, typed in a
    starting value of 0.506 instead of 0.506127. The end result was
    completely different from the first result of the simulation. Stated
    more evocatively, the theory is that a single butterfly flapping its
    wings on the other side of the world could cause a massive weather
    shift and ruin your weekend at the beach. It's called *nonlinear*
    because there isn't a linear relationship between a change in
    initial conditions and a change in outcome. A small change in
    initial conditions can have a massive effect on the outcome.
    Nonlinear systems are a superset of chaotic systems. In [Chapter
    7](/cellular-automata#section-cellular-automata), you'll see how
    even in a system of many 0s and 1s, if you change just one bit, the
    result will be completely different.
-   **Competition and cooperation:** One ingredient that often makes a
    complex system tick is the presence of both competition and
    cooperation among the elements. The upcoming flocking system will
    have three rules: alignment, cohesion, and separation. Alignment and
    cohesion will ask the elements to "cooperate" by trying to stay
    together and move together. Separation, however, will ask the
    elements to "compete" for space. When the time comes, try taking out
    just the cooperation or just the competition, and you'll see how the
    system loses its complexity. Competition and cooperation are found
    together in living complex systems, but not in nonliving complex
    systems like the weather.
-   **Feedback:** Complex systems often include a loop that feeds the
    output of the system back into the system to influence its behavior
    in a positive or negative direction. Let's say you decide to take
    public transportation to work each day because it's the most
    reliable and cost-effective solution, and you're put off by the
    traffic congestion and environmental impact of driving. You aren't
    alone; others turn to public transportation too. The system grows
    more efficient and attractive, serving more people with the same
    resources, and meanwhile, vehicle traffic is reduced. Over time,
    however, the system may struggle to accommodate the rising demand,
    leading to overcrowding, delays, and increased fares to fund
    infrastructure improvements. As a result, you and others start to
    switch back to driving, thereby increasing traffic congestion once
    again and reducing public transport's efficiency. As traffic
    worsens, the funds from increased fares are (hopefully) used to
    improve public transport infrastructure, making it more appealing
    once again. In this way, the cost and efficiency of public
    transportation are both the input of the system (determining whether
    you choose to use it or not) and the output (the degree of traffic
    congestion and subsequent cost and efficiency). Economic models are
    just one example of a human complex system. Others include fads and
    trends, elections, crowds, and traffic flow.

Complexity will serve as a key theme for much of the remainder of the
book. In this section, I'll begin by introducing an additional feature
to the `Vehicle` class: the ability to perceive neighboring vehicles.
This enhancement will pave the way for a culminating example of a
complex system in which the interplay of simple individual behaviors
results in an emergent behavior: flocking*.*

### Implementing Group Behaviors (or: Let's Not Run Into Each Other)

Managing a group of objects is certainly not a new concept. You've seen
this before---in [Chapter 4](/particles#section-particles), where I
developed the `Emitter` class to represent an overall particle system.
There, I used an array to store a list of individual particles. I'll
start with the same technique here and store `Vehicle` objects in an
array:

``` {.codesplit code-language="javascript"}
// Declare an array of Vehicle objects.
let vehicles;

function setup() {
  //{!3} Initialize and fill the array
  // with a bunch of vehicles.
  vehicles = [];
  for (let i = 0; i < 100; i++) {
    vehicles.push(new Vehicle(random(width), random(height)));
  }
}
```

Now, when it comes time to manipulate all the vehicles in `draw()`, I
can loop through the array and call the necessary methods:

``` {.codesplit code-language="javascript"}
function draw() {
  for (let vehicle of vehicles) {
    vehicle.update();
    vehicle.show();
  }
}
```

Maybe I want to add a behavior, a force to be applied to all the
vehicles. This could be seeking the mouse:

``` {.codesplit code-language="javascript"}
    vehicle.seek(mouseX, mouseY);
```

But that's an *individual* behavior, and I've already spent the bulk of
this chapter worrying about individual behaviors. You're here because
you want to apply a *group* behavior. I'll begin with **separation**, a
behavior that commands, "Avoid colliding with your neighbors!"

``` {.codesplit code-language="javascript"}
    vehicle.separate();
```

That looks good but is not quite right. What's missing? In the case of
`seek()`, I said, "Seek `mouseX` and `mouseY`." In the case of
`separate()`, I'm saying, "Separate from *everyone else*." Who is
everyone else? It's the list of all the other vehicles:

``` {.codesplit code-language="javascript"}
    vehicle.separate(vehicles);
```

This is the big leap beyond what you saw before with particle systems.
Instead of each element (particle or vehicle) operating on its own, I'm
now saying, "Hey you, that vehicle there! When it comes time for you to
operate, you need to operate with an awareness of everyone else. So I'm
going to go ahead and pass you the list of everyone else."

::: avoid-break
Putting together what I've done so far, here are the `setup()` and
`draw()` functions for a sketch that exhibits group behavior:

``` {.codesplit code-language="javascript"}
let vehicles;

function setup() {
  createCanvas(640, 240);
  vehicles = [];
  for (let i = 0; i < 100; i++) {
    vehicles.push(new Vehicle(random(width), random(height)));
  }
}

function draw() {
  background(255);

  for (let vehicle of vehicles) {
    //{!1 .bold} This is really the only new thing you’re doing in this section.  You’re asking
    // a Vehicle object to examine all the other vehicles in the process of calculating a
    // separation force.
    vehicle.separate(vehicles);
    vehicle.update();
    vehicle.show();
  }
}
```
:::

::: half-width-right
![Figure 5.32: The desired velocity for separation (equivalent to
fleeing) is a vector that points in the opposite direction of a
target.](images/05_steering/05_steering_34.png)
:::

Of course, this is just the beginning. The real work happens inside the
`separate()` method. Reynolds defines the separation behavior as "steer
to avoid crowding." In other words, if a given vehicle is too close to
you, steer away from that vehicle. Sound familiar? Remember the seek
behavior, steering a vehicle toward a target? Reverse that force and you
have the flee behavior, which is what should be applied here to achieve
separation (see Figure 5.32).

::: half-width-right
![Figure 5.33: Desired velocity for separation is the average of
multiple fleeing desired
velocities.](images/05_steering/05_steering_35.png)
:::

But what if more than one vehicle is too close? In that case, I'll
define separation as the average of all the vectors pointing away from
any close vehicles (Figure 5.33).

How do I turn that into code? Remember, I'm writing a method called
`separate()` that receives an array of `Vehicle` objects as an argument:

``` {.codesplit code-language="javascript"}
separate(vehicles) {

}
```

Inside this method, I'll loop through all the vehicles and see if any
are too close:

``` {.codesplit code-language="javascript"}
  // This variable specifies how close is too close.
  let desiredSeparation = 20;
  for (let other of vehicles) {
    //{!1 .offset} What is the distance between this vehicle and the other vehicle?
    let d = p5.Vector.dist(this.position, other.position);
    if (this !== other && d < desiredSeparation) {
      //{!1} Any code here will be executed if the vehicle is within 20 pixels.

    }
  }
```

Notice that I'm checking not only whether the distance is less than a
desired separation but also whether `this` is not equal to `other`. This
is a key element. Remember, all the vehicles are in the array; without
this extra check, the vehicle will attempt to flee from itself!

If the vehicles are too close, I compute a vector that points away from
the offending vehicle:

``` {.codesplit code-language="javascript"}
    if (this !== other && d < desiredseparation) {
      //{!2 .offset} A vector pointing away from the other’s position
      let diff = p5.Vector.sub(this.position, other.position);
      diff.normalize();
    }
```

::: avoid-break
This isn't enough. I have a fleeing vector now, but what I really need
is the average of the fleeing vectors for all the vehicles that are too
close. How do I compute an average? Add up all the vectors and divide by
the total:

``` {.codesplit code-language="javascript"}
  //{!1 .bold} Start with an empty vector.
  let sum = createVector();
  //{!1 .bold} We have to keep track of how many vehicles are too close.
  let count = 0;

  for (let other of vehicles) {
    let d = p5.Vector.dist(this.position, other.position);
    if (this !== other && d < desiredseparation) {
      let diff = p5.Vector.sub(this.position, other.position);
      diff.normalize();
      //{!2 .bold} Add all the vectors together and increment the count.
      sum.add(diff);
      count++;
    }
  }

  //{!1 .bold} Make sure that there is at least one close
  // vehicle.  You don’t want to bother doing anything
  // if nothing is too close (not to mention, you can’t
  // divide by zero!).
  if (count > 0) {
    //{.bold}
    sum.div(count);
  }
```
:::

Once I have the average vector (stored in the variable `sum`), that
vector can be scaled to the maximum speed and become the desired
velocity---the vehicle *desires* to move in that direction at maximum
speed! (In fact, I really don't have to divide by `count` anymore since
the magnitude is set manually.) And once I have the desired velocity,
it's the same old Reynolds story---steering equals desired minus
velocity:

``` {.codesplit code-language="javascript"}
  if (count > 0) {
    //{!1} Scale average to max speed
    // (this becomes desired).
    sum.setMag(this.maxspeed);
    //{!1} Reynolds’s steering formula
    let steer = p5.Vector.sub(sum, vel);
    steer.limit(this.maxforce);
    //{!1} Apply the force to the vehicle.
    this.applyForce(steer);
  }
```

The following example shows the method in its entirety.

::: {data-type="example"}
### Example 5.9: Separation {#example-59-separation}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/S7YOOYs7T"
data-example-path="examples/05_steering/noc_5_07_separation">
<img src="examples/05_steering/noc_5_07_separation/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
  separate(vehicles) {
    //{!1 .bold} The desired separation is based
    // on the vehicle’s size.
    let desiredSeparation = this.r * 2;
    let sum = createVector();
    let count = 0;
    for (let other of vehicles) {
      let d = p5.Vector.dist(this.position, other.position);
      if (this !== other && d < desiredSeparation) {
        let diff = p5.Vector.sub(this.position, other.position);
        //{!1 .bold} What is the magnitude of the p5.Vector
        // pointing away from the other vehicle?
        // The closer it is, the more the vehicle should flee.
        // The farther, the less. So the magnitude is set
        // to be inversely proportional to the distance.
        diff.setMag(1 / d);
        sum.add(diff);
        count++;
      }
    }
    if (count > 0) {
      sum.setMag(this.maxspeed);
      let steer = p5.Vector.sub(sum, this.velocity);
      steer.limit(this.maxforce);
      this.applyForce(steer);
    }
  }
```

The `separate()` method includes two extra improvements. First, the
desired separation now depends on the size of the vehicle, as opposed to
an arbitrary constant. This way, the separation behavior adapts
dynamically to the individual characteristics of the vehicles. Second,
the magnitude of the vector pointing away from a neighboring vehicle is
set to be inversely proportional to the distance. This means that the
closer the neighbor, the more the vehicle wants to flee, and vice versa.

::: {data-type="exercise"}
### Exercise 5.12 {#exercise-512}

Create a `cohere()` method that follows the opposite logic of
`separate()`: if a vehicle is beyond a certain distance, steer toward
that vehicle. This will keep the group together. (In a moment, I'll look
at what happens when both cohesion and separation play out together in
the same simulation.)
:::

::: {data-type="exercise"}
### Exercise 5.13 {#exercise-513}

Add the separation force to path following to create a simulation of
Reynolds's group path following.

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/3KSPCN0x-z"
data-example-path="examples/05_steering/exercise_5_13_crowd_path_following">
<img
src="examples/05_steering/exercise_5_13_crowd_path_following/screenshot.png" />
</div>
</figure>
:::

### Combining Behaviors

The most exciting and intriguing group behaviors come from mixing and
matching multiple steering forces. After all, how could I even begin to
simulate emergence in a complex system through a sketch that has only
one rule?

When multiple steering forces are at play, I need a mechanism for
managing them all. You may be thinking, "This is nothing new. We juggle
multiple forces all the time." You would be right. In fact, this
technique appeared as early as [Chapter 2](/forces#section-forces):

``` {.codesplit code-language="javascript"}
  let wind = createVector(0.001, 0);
  let gravity = createVector(0, 0.1);
  mover.applyForce(wind);
  mover.applyForce(gravity);
```

Here, a `Mover` object responds to two forces. This all works nicely
because of the way the `Mover` class was designed to accumulate the
force vectors into its acceleration vector. In this chapter, however,
the forces stem from the internal desires of the movers (now called
*vehicles*). And those desires can be weighted so that some hold more
sway than others. For example, consider a sketch in which all vehicles
have two desires:

-   Seek the mouse position.
-   Separate from any vehicles that are too close.

Imagine the vehicles represent a school of fish. Although the fish want
to avoid colliding with one another, their primary concern is seeking
out a food source (the mouse). Being able to adjust the weights of the
two steering forces is crucial to achieving this effect.

To begin, I'll add a method called `applyBehaviors()` to the `Vehicle`
class to manage all the behaviors:

``` {.codesplit code-language="javascript"}
applyBehaviors(vehicles) {
  this.separate(vehicles);
  this.seek(createVector(mouseX, mouseY));
}
```

Here, a single method takes care of calling the other methods that apply
the forces---`separate()` and `seek()`. I could start mucking around
within those methods to adjust the strength of the forces they're
calculating, but it might be easier to instead ask those methods to
simply calculate and return the forces. Then I can adjust the forces'
strength and apply them to the vehicle's acceleration within
`applyBehaviors()`:

``` {.codesplit code-language="javascript"}
  applyBehaviors(vehicles) {
    let separate = this.separate(vehicles);
    let seek = this.seek(createVector(mouseX, mouseY));
    //{!2} Apply the forces here since seek() and separate() no longer do so.
    this.applyForce(separate);
    this.applyForce(seek);
  }
```

Here's how this new approach changes the `seek()` method:

``` {.codesplit code-language="javascript"}
  seek(target) {
    let desired = p5.Vector.sub(target, this.position);
    desired.setMag(this.maxspeed);

    let steer = p5.Vector.sub(desired, this.velocity);
    steer.limit(this.maxforce);

    this.applyForce(steer);
    //{!1} Instead of applying the force, return the vector.
    return steer;
  }
```

This change is subtle but incredibly important: it allows the strength
of these forces to be weighted all in one place.

::: {data-type="example"}
### Example 5.10: Combining Steering Behaviors (Seek and Separate) {#example-510-combining-steering-behaviors-seek-and-separate}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/UJEwENSN3"
data-example-path="examples/05_steering/noc_5_08_separation_and_seek">
<img
src="examples/05_steering/noc_5_08_separation_and_seek/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
applyBehaviors(vehicles) {
  let separate = this.separate(vehicles);
  let seek = this.seek(createVector(mouseX, mouseY));
  //{!2 .bold} These values can be whatever you want them to be!
  // They can be variables that are customized for
  // each vehicle, or they can change over time.
  separate.mult(1.5);
  seek.mult(0.5);
  this.applyForce(separate);
  this.applyForce(seek);
}
```

In this code, I use `mult()` to adjust the forces. By multiplying each
force vector by a factor, its magnitude is scaled accordingly. These
factors (in this case, 1.5 for `separate` and 0.5 for `seek`) represent
the weight assigned to each force. However, the weights don't have to be
constants. Think about how they might vary dynamically based on
conditions within the environment or properties of the vehicle. For
example, what if the `seek` weight increases when the vehicle detects
food nearby (imagine the vehicle as a creature with a `hunger` property)
or the `separate` weight becomes larger if the vehicle enters a crowded
area? This flexibility in adjusting the weights allows for more
sophisticated and nuanced behaviors to emerge.

::: {data-type="exercise"}
### Exercise 5.14 {#exercise-514}

Modify Example 5.10 so that the behavior weights change over time. For
example, what if the weights were calculated according to a sine wave or
Perlin noise? Or what if some vehicles are more concerned with seeking
and others are more concerned with separating? Can you introduce other
steering behaviors as well?
:::

### Flocking

**Flocking** is a group animal behavior found in many living creatures,
such as birds, fish, and insects. In 1986, Reynolds created a computer
simulation of flocking behavior and documented the algorithm in his
paper "Flocks, Herds, and Schools: A Distributed Behavioral Model."
Re-creating this simulation in p5.js will bring together all the
concepts in this chapter:

1.  I will use the steering force formula (steer = desired -- velocity)
    to implement the rules of flocking.
2.  These steering forces will be group behaviors and will require each
    vehicle to perceive all the other vehicles.
3.  I will combine and weight multiple forces.
4.  The result will be a complex system---intelligent group behavior
    will emerge from the simple rules of flocking without the presence
    of a centralized system or leader.

The good news is, I've already demonstrated items 1 through 3 in this
chapter, so this section can just be about putting it all together and
seeing the result.

Before I begin, I should mention that I'm going to change the name of
the `Vehicle` class (yet again). Reynolds uses the term *boid* (a
made-up word that refers to a birdlike object) to describe the elements
of a flocking system. I'll do the same.

Three rules govern flocking:

-   **Separation** (aka avoidance): Steer to avoid colliding with your
    neighbors.
-   **Alignment** (aka copy): Steer in the same direction as your
    neighbors.
-   **Cohesion** (aka center): Steer toward the center of your neighbors
    (stay with the group).

Figure 5.34 illustrates these rules.

![Figure 5.34: The three rules of flocking: separation, alignment, and
cohesion. The example vehicle and desired velocity are
bold.](images/05_steering/05_steering_36.png)

Just as with Example 5.10, in which I combined separation and seeking, I
want the `Boid` objects to have a single method that manages all three
behaviors. I'll call it `flock()`:

``` {.codesplit code-language="javascript"}
  flock(boids) {
    //{!3} The three flocking rules
    let separation = this.separate(boids);
    let alignment = this.align(boids);
    let cohesion = this.cohere(boids);
    //{!3} Arbitrary weights for these forces (try different ones!)
    separation.mult(1.5);
    alignment.mult(1.0);
    cohesion.mult(1.0);
    //{!3} Apply all the forces.
    this.applyForce(separation);
    this.applyForce(alignment);
    this.applyForce(cohesion);
  }
```

Now, it's just a matter of implementing the three rules. I applied
separation already; it's identical to the previous example. Instead,
I'll focus on alignment, or steering in the same direction as the
neighboring boids. As with all other steering behaviors, I have to
express this concept as a desire: the boid's desired velocity is the
average velocity of its neighbors. The algorithm is therefore to
calculate the average velocity of all the other boids and set that to
the desired velocity:

``` {.codesplit code-language="javascript"}
  align(boids) {
    // Add up all the velocities and divide by the total
    // to calculate the average velocity.
    let sum = createVector(0, 0);
    for (let other of boids) {
      sum.add(other.velocity);
    }
    sum.div(boids.length);
    // The vehicle desires to go in that direction at maximum speed.
    sum.setMag(this.maxspeed);
    //{!3} Reynolds’s steering force formula
    let steer = p5.Vector.sub(sum, this.velocity);
    steer.limit(this.maxforce);
    return steer;
  }
```

This is pretty good but is missing one rather crucial detail. One of the
key principles behind complex systems like flocking is that the elements
(in this case, boids) have *short-range* relationships. Thinking about
ants again, it's easy to imagine an ant being able to sense its
immediate environment, but less so an ant having an awareness of what
another ant is doing hundreds of feet away. Indeed, the ants' ability to
manifest such complex collective behavior from only these neighboring
relationships is what makes them so exciting in the first place.

In the `align()` method, I'm currently taking the average velocity of
*all* the boids, whereas I should really be looking at only the boids
within a certain distance (see Figure 5.35). That distance threshold can
be variable, of course. You could design boids that can see only 20
pixels away or boids that can see 100 pixels away.

![Figure 5.35: The example vehicle (bold) interacts with only the
vehicles within its neighborhood (the
circle).](images/05_steering/05_steering_37.png)

I already applied similar logic when I implemented separation,
calculating a force based only on other vehicles within a certain
distance. Now I want to do the same for alignment (and eventually,
cohesion):

::: avoid-break
``` {.codesplit code-language="javascript"}
  align(boids) {
    //{!1} This is an arbitrary value that could vary from boid to boid.
    let neighborDistance = 50;
    let sum = createVector(0, 0);
    let count = 0;
    for (let other of boids) {
      let d = p5.Vector.dist(this.position, other.position);
      if ((this !== other) && (d < neighborDistance)) {
        sum.add(other.velocity);
        //{!1} For an average, keep track of
        // how many boids are within the distance.
        count++;
      }
    }
    if (count > 0) {
      sum.setMag(this.maxspeed);
      let steer = p5.Vector.sub(sum, this.velocity);
      steer.limit(this.maxforce);
      return steer;
    //{!3} If no close boids are found, the steering force is zero.
    } else {
      return createVector(0, 0);
    }
  }
```
:::

As with the `separate()` method, I've included the condition
`this !== other` to ensure that a boid doesn't consider itself when
calculating the average velocity. It would probably work regardless, but
having each boid constantly be influenced by its own velocity could lead
to a feedback loop that would disrupt the overall behavior.

:::: {data-type="exercise"}
### Exercise 5.15 {#exercise-515}

::: half-width-right
![image](images/05_steering/05_steering_38.png)
:::

Can you rewrite the `align()` method so that boids see only other boids
that fall within a direct line of sight?
::::

The code for cohesion is quite similar to that for alignment. The only
difference is that instead of calculating the average *velocity* of the
boid's neighbors, I want to calculate the average *position* of the
boid's neighbors (and use that as a target to seek).

::: avoid-break
``` {.codesplit code-language="javascript"}
  cohesion(boids) {
    let neighborDistance = 50;
    let sum = createVector(0, 0);
    let count = 0;
    for (let other of boids) {
      let d = p5.Vector.dist(this.position, other.position);
      if ((this !== other) && (d < neighborDistance)) {
        //{!2} Add up all the others’ positions.
        sum.add(other.position);
        count++;
      }
    }
    if (count > 0) {
      sum.div(count);
      //{!1 .bold} Use the seek() function 
      // from Example 5.10.  The target
      // to seek is the average position of
      // your neighbors.
      return this.seek(sum);
    } else {
      return createVector(0, 0);
    }
  }
```
:::

It's also worth taking the time to write a class called `Flock` that
manages the whole group of boids. It will be virtually identical to the
`ParticleSystem` class from [Chapter 4](/particles#section-particles),
with only one tiny change: when I call `run()` on each `Boid` object (as
I did to each `Particle` object), I'll pass in a reference to the entire
array of boids:

``` {.codesplit code-language="javascript"}
class Flock {
  constructor() {
    this.boids = [];
  }

  run() {
    for (let boid of this.boids) {
      //{!1 .bold} Each Boid object must know about
      // all the other boids.
      boid.run(this.boids);
    }
  }

  addBoid(boid) {
    this.boids.push(boid);
  }
}
```

All that remains is to initialize the flock in `setup()` and run it in
`draw()`.

::: {data-type="example"}
### Example 5.11: Flocking {#example-511-flocking}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/IkpBw96Sd"
data-example-path="examples/05_steering/example_5_9_flocking">
<img src="examples/05_steering/example_5_9_flocking/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
//{!1} A Flock object manages the
// entire group.
let flock;

function setup() {
  createCanvas(640, 240);
  flock = new Flock();
  for (let i = 0; i < 120; i++) {
    let boid = new Boid(width / 2, height / 2);
    //{!1} The flock starts out with 120 boids.
    flock.addBoid(boid);
  }
}

function draw() {
  background(255);
  flock.run();
}
```

Just as with the particle systems from [Chapter
4](/particles#section-particles), you can see the elegance of OOP in
simplifying the `setup()` and `draw()` functions.

::: {data-type="exercise"}
### Exercise 5.16 {#exercise-516}

Combine flocking with other steering behaviors.
:::

:::: {data-type="exercise"}
### Exercise 5.17 {#exercise-517}

::: half-width-right
![image](images/05_steering/05_steering_39.png)
:::

In his book *The Computational Beauty of Nature* (Bradford Books, 2000),
Gary Flake describes a fourth rule for flocking, **view**: "Move
laterally away from any boid that blocks the view." Have your boids
follow this rule.
::::

::: {data-type="exercise"}
### Exercise 5.18 {#exercise-518}

Create a flocking simulation in which all the parameters (*separation
weight*, *cohesion weight*, *alignment weight*, *maximum force*,
*maximum speed*) change over time. They could be controlled by Perlin
noise or by user interaction. (For example, you could use the p5.js
`createSlider()` function to tie the values to slider positions that can
be adjusted in real time.)
:::

::: {data-type="exercise"}
### Exercise 5.19 {#exercise-519}

Visualize the flock in an entirely different way.
:::

## Algorithmic Efficiency (or: Why Does My Sketch Run So Slowly?)

Group behaviors are wonderful, but it's with a heavy heart that I must
admit that they can also be slow. In fact, the bigger the group, the
slower the sketch can be. I'd love to hide this dark truth from you,
because I'd like you to be happy and live a fulfilling and meaningful
life, free from concerns about the efficiency of your code. But I'd also
like to be able to sleep at night without worrying about your inevitable
disappointment when you try to run your flocking simulation with too
many boids.

Usually, when I talk about p5.js sketches running slowly, it's because
drawing to the canvas can be slow---the more you draw, the slower your
sketch runs. As you may recall from [Chapter
4](/particles#section-particles), switching to a different renderer like
WebGL can sometimes alleviate this issue, allowing for faster drawing of
larger particle systems. With something like a flocking simulation,
however, the slowness derives from the algorithm. Computer scientists
put this problem in terms of something called **big**
[O]{data-type="equation"} **notation**, where the
[O]{data-type="equation"} stands for *order*. This is shorthand for
describing the efficiency of an algorithm: How many computational cycles
does the algorithm require to complete?

Consider a simple search problem. You have a basket containing 100
chocolate treats, only one of which is pure dark chocolate. That's the
one you want to eat. To find it, you pick the chocolates out of the
basket one by one. You might be lucky and find it on the first try, but
in the worst-case scenario, you have to check all 100 before you find
the dark chocolate. To find one thing in 100, you have to check 100
things (or to find one thing in [N]{data-type="equation"} things, you
have to check [N]{data-type="equation"} times). The big
[O]{data-type="equation"} notation here is [O(N)]{data-type="equation"}.
This, incidentally, is also the big [O]{data-type="equation"} notation
that describes a simple particle system. If you have
[N]{data-type="equation"} particles, you have to run and display those
particles [N]{data-type="equation"} times.

Now, let's think about a group behavior such as flocking. For every
`Boid` object, you have to check the velocity and position of every
other `Boid` object before you can calculate its steering force. Let's
say you have 100 boids. For boid 1, you need to check 100 boids; for
boid 2, you need to check 100 boids; and so on. In all, for 100 boids,
you need to perform 10,000 checks ([100 \\times 100 =
\\text{10,000}]{data-type="equation"}).

You might be thinking, "No problem. Computers are fast. They can do
10,000 things pretty easily." But what if there are 1,000 boids? Then
you have this:

::: {data-type="equation"}
\\text{1,000} \\times \\text{1,000} = \\text{1,000,000 cycles}
:::

This is getting rather slow but is still somewhat manageable. What about
10,000 elements?

::: {data-type="equation"}
\\text{10,000} \\times \\text{10,000} = \\text{100,000,000 cycles}
:::

Now things are getting really slow. Really, really, really slow.

Notice a pattern? As the number of elements increases by a factor of 10,
the number of required cycles increases by a factor of 100. More
broadly, as the number of elements increases by a factor of
[N]{data-type="equation"}, the cycles increase by a factor of [N \\times
N]{data-type="equation"}, or [N\^2]{data-type="equation"}. In big
[O]{data-type="equation"} notation, this is known as
[O(N\^2)]{data-type="equation"}.

Perhaps you're thinking, "No problem. With flocking, I need to consider
only the boids that are close to the current boid. So even if I have
1,000 boids, I can just look at, say, the 5 closest boids to each one,
and then I only have 5,000 cycles." You pause for a moment and then
start thinking, "So for each boid, I just need to check all the boids
and find the 5 closest ones and I'm good!" See the catch-22? Even if you
want to look at only the close ones, the only way to know what the close
ones are would be to check all of them.

Or is there another way?

### Spatial Subdivisions

In his 2000 paper ["Interaction with Groups of Autonomous
Characters"](https://www.red3d.com/cwr/papers/2000/pip.pdf), Reynolds
(surprise, surprise) suggests a technique known as **bin-lattice spatial
subdivision** (often called *binning* for short) for optimizing flocking
algorithms and other group behaviors. This technique hinges on dividing
the simulation space into a grid of smaller cells (or bins).

To demonstrate, imagine the canvas is divided into a grid of 10 rows and
10 columns, for a total of 100 cells ([10 \\times 10 =
100]{data-type="equation"}). And let's say you have 2,000 boids---a
number small enough for you to realistically want, but large enough to
run too slowly ([\\text{2,000} \\times \\text{2,000} = \\text{4,000,000
cycles)}]{data-type="equation"}. At any given moment, each boid falls
within a cell in the grid, as shown in Figure 5.36. With 2,000 boids and
100 cells, on average there will be approximately 20 boids per cell
([\\text{2,000} \\div 100 = 20]{data-type="equation"}).

![Figure 5.36: A square canvas full of vehicles, subdivided into a grid
of square cells](images/05_steering/05_steering_40.png)

Now say that in order to apply the flocking rules to a given boid, you
need to look at only the other boids that are in that boid's cell. With
an average of 20 boids per cell, each cell would require 400 cycles ([20
\\times 20 = 400]{data-type="equation"}), and with 100 cells, that's
40,000 cycles total ([400 \\times 100 =
\\text{40,000}]{data-type="equation"}). That's a massive savings of over
4,000,000 cycles!

To implement the bin-lattice spatial subdivision algorithm in p5.js,
I'll need multiple arrays. The first array keeps track of all the boids,
just as in the original flocking example:

``` {.codesplit code-language="javascript"}
let boids = [];
```

The second is a 2D array (repurposing the code from Example 5.4)
representing the cells in the grid:

``` {.codesplit code-language="javascript"}
// Each cell is 40×40 pixels.
let resolution = 40;
// How many columns and rows are in the grid, based on the width and height?
let cols = floor(width / resolution);
let rows = floor(height / resolution);
// Create the 2D array.
let grid = new Array(cols);
for (let i = 0; i < grid.length; i++) {
  grid[i] = new Array(rows);
}
```

Each value in the 2D array is itself an array that will hold references
to the `Boid` objects currently inside that cell in the grid. If you're
keeping score, that's an array within an array within an array:

``` {.codesplit code-language="javascript"}
  for (let i = 0; i < cols; i++) {
    for (let j = 0; j < rows; j++) {
      //{!1} An array for every cell in the grid
      grid[i][j] = [];
    }
  }
```

Every cycle through `draw()`, the array for each grid cell is first
cleared. Then each boid registers itself in the appropriate cell
according to its position. This way, the boids' cell assignments are
updated as the boids move:

::: snip-below
``` {.codesplit code-language="javascript"}
function draw() {
  // Each frame, the grid is reset to empty arrays.
  for (let i = 0; i < cols; i++) {
    for (let j = 0; j < rows; j++) {
      grid[i][j] = [];
    }
  }

  // Place each boid into the appropriate cell in the grid.
  for (let boid of flock.boids) {
    // Find the right column and row.
    let column = floor(boid.position.x / resolution);
    let row = floor(boid.position.y / resolution);
    // Constrain to the limits of the array.
    column = constrain(column, 0, cols - 1);
    row = constrain(row, 0, rows - 1);
    // Add the boid.
    grid[column][row].push(boid);
  }
```
:::

Finally, when it comes time to have the boids check their neighbors,
they can look at only those in their particular cell.

::: {data-type="example"}
### Example 5.12: Bin-Lattice Spatial Subdivision {#example-512-bin-lattice-spatial-subdivision}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/QXONgLiJ0"
data-example-path="examples/05_steering/example_5_9_flocking_with_binning">
<img
src="examples/05_steering/example_5_9_flocking_with_binning/screenshot.png" />
</div>
</figure>
:::

``` {.codesplit code-language="javascript"}
  run(boids) {
    let column = floor(this.position.x / resolution);
    let row = floor(this.position.y / resolution);
    column = constrain(column, 0, cols - 1);
    row = constrain(row, 0, rows - 1);
    // Only these boids will be checked. See the code online for how neighboring cells are also included.
    let neighbors = grid[column][row]; 
    this.flock(neighbors);
    this.update();
    this.borders();
    this.render();
  }
```

I'm covering only the basics of the bin-lattice algorithm here. In
practice, each boid should also check the boids in the neighboring cells
(above, below, left, right, and diagonals), as well as the boids in its
own cell. (To find out how that's done, see the full code on the book's
website.) Even with that extra checking, however, the algorithm is still
much more efficient than checking every single boid.

This approach still has flaws, however. For example, what if all the
boids congregate in the corner and live in the same cell? Doesn't that
take me right back to checking all 2,000 against all 2,000? In fact,
bin-lattice spatial subdivision is most effective when the elements are
evenly distributed throughout the canvas. A data structure known as a
**quadtree**, however, can handle unevenly distributed systems,
preventing the worst-case scenario of all the boids crowding into a
single cell.

The quadtree expands the spatial subdivision strategy by dynamically
adapting the grid according to the distribution of the boids. Instead of
a fixed grid, a quadtree starts with a single large cell that
encompasses the entire space. If too many boids are found within this
cell, it splits into four smaller cells. This process can repeat for
each new cell that gets too crowded, creating a flexible grid that
provides finer resolution when and where it's needed.

::: {data-type="example"}
### Example 5.13: Quadtree {#example-513-quadtree}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/J5XVww9xQ"
data-example-path="examples/05_steering/5_11_quadtree_part_1">
<img src="examples/05_steering/5_11_quadtree_part_1/screenshot.png" />
</div>
</figure>
:::

The quadtree data structure is key to the Barnes-Hut algorithm, which I
referenced briefly when building an *n*-body simulation in [Chapter
2](/forces#section-forces). This method uses a quadtree to approximate
groups of bodies into a single one when calculating gravitational
forces. This drastically reduces the number of calculations needed,
allowing simulations with large numbers of bodies to run more
efficiently. You can learn more about [building a quadtree and applying
it to a flocking system as part of Coding Challenge #98 on the Coding
Train website](https://thecodingtrain.com/quadtree).

::: {data-type="exercise"}
### Exercise 5.20 {#exercise-520}

Expand the bin-lattice spatial subdivision flocking sketch from Example
5.12 to use a quadtree.
:::

### More Optimization Tricks

While I'm at it, here are a few more tips related to keeping your code
in tip-top, speedy shape:

-   Use the magnitude squared (or sometimes the distance squared).
-   Calculate the sine and cosine lookup tables.
-   Don't make gazillions of unnecessary p5.Vector objects.

Each of these tips is detailed next.

#### Use the Magnitude Squared

What is magnitude squared, and when should you use it? Think back to how
the magnitude of a vector is calculated.

::: avoid-break
``` {.codesplit code-language="javascript"}
function mag() {
  return sqrt(x * x + y * y);
}
```
:::

Magnitude requires the square-root operation. And so it should! After
all, if you want the magnitude of a vector, you have to break out the
Pythagorean theorem (we did this in [Chapter
1](/vectors#section-vectors)). However, if you could somehow skip taking
the square root, your code would run faster.

Say you just want to know the *relative* magnitude of a vector `v`. For
example, is the magnitude greater than 10?

``` {.codesplit code-language="javascript"}
if (v.mag() > 10) {
  /* Do something! */
}
```

Well, that is equivalent to saying the following:

``` {.codesplit code-language="javascript"}
if (v.magSq() > 100) {
  /* Do something! */
}
```

And how is magnitude squared calculated?

``` {.codesplit code-language="javascript"}
function magSq() {
  return x * x + y * y;
}
```

It's calculated the same as magnitude, but without the square root. In
the case of a single vector, using `magSq()` rather than `mag()` will
never significantly improve the performance of a p5.js sketch. However,
if you're computing the magnitude of thousands of vectors each time
through `draw()`, working with the magnitude squared could help your
code run a wee bit faster.

#### Calculate Sine and Cosine Lookup Tables

Taking the square root isn't the only mathematical function that's slow
to compute. Trig functions like sine, cosine, and tangent are also slow.
If you just need an individual sine or cosine value here or there in
your code, you're never going to run into a problem. But what if you had
something like this?

::: avoid-break
``` {.codesplit code-language="javascript"}
function draw() {
  for (let i = 0; i < 10000; i++) {
    print(sin(PI));
  }
}
```
:::

Sure, this is a totally ridiculous code snippet that you would never
write. But it illustrates a certain point: if you're calculating the
sine of pi 10,000 times, why not just calculate it once, save that
value, and refer to it whenever necessary?

This is the principle behind sine and cosine **lookup tables**. Instead
of calling the sine and cosine functions in your code whenever you need
them, you can build an array that stores the results of sine and cosine
at angles from [0]{data-type="equation"} to
[2\\pi]{data-type="equation"}, and then just look up the precalculated
values when you need them. For example, here are two arrays that store
the sine and cosine values for every integer angle from 0 to 359
degrees. I'll use `angleMode(DEGREES)` here to simplify the discussion,
but the same technique can be applied with radians:

``` {.codesplit code-language="javascript"}
angleMode(DEGREES);
let sinvalues = [];
let cosvalues = [];
for (let i = 0; i < 360; i++) {
  sinvalues[i] = sin(i);
  cosvalues[i] = cos(i);
}
```

Now, what if you need to print the sine of pi (or 180 degrees)?

``` {.codesplit code-language="javascript"}
let angle = 180;
for (let i = 0; i < 10000; i++) {
  print(sinvalues[angle]);
}
```

The key here is that looking up a precalculated value from an array is
incredibly fast compared to a complex operation like sine or cosine.

::: {data-type="example"}
### Example 5.14: Sin/Cos Lookup Table {#example-514-sincos-lookup-table}

<figure>
<div data-type="embed"
data-p5-editor="https://editor.p5js.org/natureofcode/sketches/uqRJyVq8g"
data-example-path="examples/05_steering/example_5_12_sine_cosine_lookup_table">
<img
src="examples/05_steering/example_5_12_sine_cosine_lookup_table/screenshot.png" />
</div>
</figure>
:::

The code accompanying Example 5.14 enhances the initial snippets by
incorporating variables for the lookup table's precision, allowing it to
store values at increments of less than 1 degree.

#### Don't Make Gazillions of Unnecessary p5.Vector Objects {#dont-make-gazillions-of-unnecessary-p5vector-objects}

In any sketch, every object you create occupies space in the computer's
memory. This might not be a concern with just a few objects, but when
sketches generate many objects, especially in loops or over time, it can
slow performance. Sometimes it turns out that not all the objects are
really necessary.

I have to admit, I'm perhaps the biggest culprit when it comes to
creating excessive objects. In the interest of writing clear and
understandable examples, I often choose to make extra `p5.Vector`
objects when I absolutely don't need to. For the most part, this isn't a
problem at all. But sometimes it can be. Take a look at this example:

``` {.codesplit code-language="javascript"}
function draw() {
  for (let v of vehicles) {
    let mouse = createVector(mouseX, mouseY);
    v.seek(mouse);
  }
}
```

Say the `vehicles` array contains 1,000 vehicles. That means I'm also
making 1,000 new `p5.Vector` objects for the mouse's position every
single time through `draw()`. On any standard laptop or desktop computer
purchased in recent times, this sketch likely won't register a
complaint, run slowly, or have any problems. After all, modern computers
have tons of RAM, and JavaScript will be able to handle making and
disposing of 1,000 or so temporary objects without much of a problem.

If, however, the number of objects grows larger (and it easily could), a
problem will almost certainly arise. As such, you should look for ways
to reduce the number of `p5.Vector` objects you make. In this case,
here's a simple fix:

``` {.codesplit code-language="javascript"}
function draw() {
  let mouse = createVector(mouseX, mouseY);
  for (let v of vehicles) {
    v.seek(mouse);
  }
}
```

Now I've made just 1 vector instead of 1,000. Even better, I could turn
the vector into a global variable and then just assign the `x` and `y`
values within `draw()` with `set()`:

``` {.codesplit code-language="javascript"}
let mouse;

function setup() {
  mouse = createVector();
}

function draw() {
  mouse.set(mouseX, mouseY);
  for (let v of vehicles) {
    v.seek(mouse);
  }
}
```

Now I never make a new `p5.Vector` object after the sketch starts; I
just use the same one over the whole length of the sketch!

Throughout the book's examples, you'll find lots of opportunities to
reduce the number of temporary objects. (I told you, I'm a major
offender.) For example, here's a snippet from this chapter's `seek()`
method:

``` {.codesplit code-language="javascript"}
    let desired = p5.Vector.sub(target, this.position);
    desired.normalize();
    desired.mult(this.maxspeed);
    //{!1 .bold} Create a new vector to store the steering force.
    let steer = p5.Vector.sub(desired,this.velocity);
    steer.limit(this.maxforce);
    return steer;
```

See how I've made two vector objects? First, I calculate the desired
velocity vector, then the steering force. To be more efficient, I could
rewrite this to create only one vector:

``` {.codesplit code-language="javascript"}
    let desired = p5.Vector.sub(target, this.position);
    desired.normalize();
    desired.mult(this.maxspeed);
    //{!3 .bold} Calculate the steering force in the desired vector.
    desired.sub(this.velocity);
    desired.limit(this.maxforce);
    return desired;
```

I don't actually need a second vector called `steer`. I can reuse the
`desired` vector object and turn it into the steering force by
subtracting `velocity`. I didn't do this in my example because it makes
the code more confusing to read. But in some cases, changes like this
may improve efficiency.

::: {data-type="exercise"}
### Exercise 5.21 {#exercise-521}

Eliminate as many temporary `p5.Vector` objects from the flocking
example as possible. Also use `magSq()` where possible.
:::

::: {data-type="project"}
### The Ecosystem Project {#the-ecosystem-project-6}

Use steering forces to drive the behavior of the creatures in your
ecosystem. Here are some possibilities:

-   Create schools or flocks of creatures.
-   Use a seeking behavior for creatures to search for food (for chasing
    moving prey, consider *pursuit*).
-   Use a flow field for the ecosystem environment. For example, how
    does your system behave if the creatures live in a flowing river?
-   Build a creature with countless steering behaviors (as many as you
    can reasonably add). Think about ways to vary the weights of the
    behaviors so you can dial them up and down, mixing and matching on
    the fly. How are creatures' initial weights set? What rules drive
    how the weights change over time?
-   Complex systems can be nested. Can you design a single creature out
    of a flock of boids? And can you then make a flock of those
    creatures?
-   Complex systems can have memory (and be adaptive). Can the history
    of your ecosystem affect the behavior in its current state? (This
    could be the driving force behind how the creatures adjust their
    steering force weights.)

![image](images/05_steering/05_steering_41.png)
:::
::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::::
