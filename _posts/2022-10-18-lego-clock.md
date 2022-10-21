---
layout: post
title: "Building a pendulum clock out of Lego"
date: 2022-10-18 12:24:00 -0700
---

{% include latex.html %}

![Lego pendulum clock render](/img/lego-clock/full clock face.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

Behold, I have created a weight-driven pendulum clock to hang on my wall, 100% out of lego parts!

![Lego clock gear train](/img/lego-clock/PXL_20221020_215518572.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The entire gear train is very compact; everything including the drive train is packed into a 7x11x9 stud volume, roughly the size of a small grapefruit.

It has around **24h of autonomy between windings** at the height I have it currently mounted. It's also **accurate to within 1 minute per day**. In fact, its more accurate than my consumer wrist watch[^1].

It looks nice enough that I'm proud to display it in my living room. All the gears and guts are exposed for the world to see and it has easily readablehour markers.

Here's the [stud.io model file](/static/jasty-lego-clock.io) in case you're curious about building your own pendulum clock. Keep reading to learn more about how I designed this clock and how it works!

![Lego pendulum clock](/img/lego-clock/PXL_20221020_222852016.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

## Inspiration

Growing up, my grandma had a wooden pendulum clock above her fireplace that now lives in my parents' living room, and the soft tick-tock that it makes is comforting and nostalgic.

After seeing some 3D print designs showcasing the basics of watch movements, I became curious. Is it possible to 3D print a clock? But consumer 3D printers may not have enough resolution, and the required tolerances seem well beyond their capabilities.

What about DIYing one out of Lego? Can one be built that:
* is a practical time-keeping device
* satisfies my craving for ambient tick-tock-ing
* looks nice enough to hang on my wall

After some quick googling, turns out I'm not the first person to attempt this kind of project. There were already some really great Lego clocks online that I drew inspiration from:
* [Nine Complication Lego Clock](https://youtu.be/996ApxqTBj0)
* [LEGO Mangle rack clock](https://youtu.be/GUdlSYC1cCE)
* [Amazing LEGO Technic Grand Complication Clock](https://youtu.be/9osRb6Sbuaw)
* [KEvronista](https://www.youtube.com/user/KEvronista)
* [Dillon Sharlet's Practical LEGO Pendulum Clock](https://dsharlet.com/2017/11/04/practical-lego-pendulum-clock.html)
* [Darrel Aldrich](https://www.youtube.com/channel/UCakfCvl6URtj52IxyO6_vhw)
* [David Ziemkiewicz](https://www.youtube.com/channel/UC4QlE2qOMf9Ha2m0RdWyvJg)
* [University of Cambridge: The prodigious pendulum clock](http://mi.eng.cam.ac.uk/IALego/clock.html)

I particularly love the look of Darrel Aldrich's grandfather clock. I think he nailed the hands, face, and look of an ornately carved wooden body. In fact, my ideal Lego clock would have a very similar look, but in a smaller wall-mounted form factor (more like a [Regulator style](https://commons.wikimedia.org/wiki/File:GB-3-Gew-Pendeluhr_(Luekk).jpg#/media/File:GB-3-Gew-Pendeluhr_(Luekk).jpg)), but I'd also settle for something bare-bones with the guts exposed.

Seeing that others have succeeded already, I knew what I wanted was achievable. Those were also great learning resources, since I had zero knowledge of how mechanical clocks worked going into this.

## Design Goals and Constraints

As stated previously, I wanted this to be a practical time keeping device. For me, that means I can't be winding this thing more than once per day. I also need to be able to rely on the displayed time being correct, and I don't want to be setting the time every day. To me that means the displayed time can't be off by more than around 1 minute.

The clock has to sound nice, too. I wanted something to tickle my nostalgia, so it must audibly tick. Moreover, I wanted something that makes a sound on each second -- a 0.5s, 1s, or 2s pendulum would work, but a 1.5s pendulum wouldn't. A half-second pendulum is too fast and sounds frantic; a two-second pendulum (like what a grandfather clock would have) would be too long and look out of place. That just leaves a one-second pendulum as the way to go.

To look nice, the clock needs to be short enough to hang on the wall around eye level, say around 5ft/1.5m off the ground. Also, the movement needs to be horizontally symmetric in certain ways -- the hands, pendulum, and weights should be centered horizontally. It should also have some form of face with at least hour markers. And ideally it should be small enough to jam inside of a wooden-looking body if desired.

In summary:
* run at least 24h between windings
* use around 1.5m of drop distance
* 1 minute per day accuracy
* 1 second pendulum
* hands, pendulum, and weights visually centered
* face with hour markers
* compact

### Concrete Requirements

We can actually put concrete numbers to some of the design constraints.

Assuming we're using a **40-tooth gear as the escapement wheel**[^2] and a **1s pendulum**:
  * escapement wheel rotates 1 tooth per second $$ = \frac{1}{40} Hz $$
  * minute hand rotates once every 3600 seconds $$ = \frac{1}{3600} Hz $$
  * **90x escapement wheel to minute hand gear reduction**: $$ \frac{3600}{40} = 90 $$

[Lego gears have a pitch (distance between teeth) of π mm](https://orionrobots.co.uk/wiki/lego_specifications.html). Also, [these chain links](https://www.bricklink.com/v2/catalog/catalogitem.page?P=3711&idColor=11) mesh with every other gear tooth, meaning connected chain links have a 2π mm pitch or approximately 6.28mm.
  * 1m of drop distance ≈ 160 chain links
  * **24h of runtime / 1.5m of drop = 16h/m ≈ 1h per 10 chain links**

Further assuming we're driving the clock with the chain attached to a **24-tooth drive gear**[^3]:
  * 24 teeth = 12 chain links
  * drive gear rotates 10 chain links per hour $$ = \frac{1}{4320} Hz $$
  * **about 108x escapement wheel to drive gear reduction**: $$ \frac{4320}{40} = 108 $$

With a constant drive force (the drive weight is just pulled down by gravity) we can calculate the power consumption of a clock using the formula $$ P = \frac{W}{\Delta t} = m g v $$. With 24h runtime and 1.5m of drop, the relationship between drive weight and power consumption is $$ P \approx (170 e^{-6} \frac{m^{2}}{s^{3}}) mass $$.

Based on [prior art](https://dsharlet.com/2017/11/04/practical-lego-pendulum-clock.html) a power consumption of around 25 μW is realistic. Assuming that level of efficiency, the power formulas above tell us how much weight we need: to achieve **25 μW of power consumption**, **24h of runtime**, and **1.5m of drop** requires **176g or 3-4 [weight bricks](https://www.bricklink.com/v2/catalog/catalogitem.page?P=73090b)** each weighing about 53g.

Thanks to [Brick Experiment Channel](https://www.youtube.com/watch?v=7YGp5zebQFE), we can also put an upper bound on how much weight to use. In that video, **standard axles start to permanently deform between 0.11 N m and 0.18 N m of torque** which means **no more than 0.94kg of weight or 18 weight bricks** on a 24-tooth gear (12mm pitch radius).

## Computing Lego Gear Trains

Lego gears only come in certain sizes, and given two lego gears, **there are limited ways to arrange them such that they'll mesh together**.

[https://marian42.de/gears/](https://marian42.de/gears/) is an excellent website that will tell you which gears you can use to achieve a certain gear ratio, and how to mesh gear pairs together.

But reality is worse: not only are there limited ways that gears mesh, but being physical objects, Legos also take up physical space 🤮

Thus I wanted **better tooling**. Telling me how a particular pair of gears will mesh is nice, and telling me a possible sequence of gears is nice too, but that still leaves a lot of bin packing to arrange the entire gear train in 3D. I wanted **software to assist in computing and arranging the *entire* gear train**, given some of the constraints imposed by how the clock is supposed to work.

The constraints for this clock:
* physical viability
  * unless gears are supposed to mesh together, they can't occupy the same space as another gear or axle
  * gears that are supposed to mesh together should be the right distance apart
* gear ratio
  * the minute hand -> hour hand gear ratio must be 12x
  * the escapement -> minute hand gear ratio must be 90x
* horizontal/vertical displacement
  * for coaxial hands, the minute hand -> hour hand gearing must start and end on the same axle
  * for a centered escapement/pendulum, the minute hand -> escapement gearing must start and end in the same horizontal position
* some pre-determined gears in the train
  * the hour hand is connected to a 16-tooth clutch gear
  * the escapement wheel is a 40-tooth gear
  * there should be a friction-pin-clutch-compatible gear connecting the minute hand to the rest of the gear train, to allow hand setting
* some other physical constraints
  * for coaxial hands, the gear train needs to be out of the way of the main axle that punches through the whole train
  * the minute -> hour hand gear train should have an even number of gear pairs so that the hands rotate in the same direction

Also, I'm not sure what criteria are used to sort the gear trains in various websites, but I wanted to play around with the **sorting to surface the "optimal" gear trains and placements**.

So, I wrote some python scripts to do all that! Did I mention that my background is in software? :)

## How To Compute The Best Gear Trains Using Python

The scripts are in my [`weinstein/lego-gear-calculator` repository on github](https://github.com/weinstein/lego-gear-calculator).

The high level overview of how it works:
  1. Find all the gear trains that achieve the target gear ratio
  2. For each gear train, find all the ways those pairs of gears can be placed in 2D so that the pairs mesh
  3. Use a greedy algorithm to determine the z-placement of gears on axles [^4]
  4. Sort the results by criteria such as: number of gears, volume required, etc

Here's the most interesting snippet which expresses all the constraints mentioned above and generates the actual clock gear train arrangements:
```python
# Calculates all the 3D arrangements of the entire gear train for a clock.
# There are 2 sub trains:
#   * hour hand -> minute hand
#   * minute hand -> escapement
# This calculates gear trains for the subtrains individually, then glues
# them together and calculates z placement for the glued trains.
def total_clock():
    hands = solve(
            ratio=12,
            min_dx=0, max_dx=0,
            min_dy=0, max_dy=0,
            max_depth=5,
            spacing=1,
            err=0.05,
            # Coaxial minute/hour hands:
            #  must start with 16t clutch,
            #  and must have the same direction of rotation for input/output
            train_filter=lambda t: t[0][0] == 16 and len(t) % 2 == 0)
    hands_axles = (list(position_axles(a)) for a in hands)
    # Coaxial minute/hour hands constraint:
    # minute hand must be able to route through the entire arrangement
    hands_axles = filter(lambda x: clear_for_axle(x[1:-1], 0, 0), hands_axles)

    def _insert_clutchable(t):
        # A 40t or 36t gear can be used to make the first axle into a friction
        # axle.
        # A 16t clutch gear linkage in the first pair can be used as a friction
        # clutch connecting to the minute hand.
        if t[1][0] == 40 or t[1][0] == 36 or t[0][1] == 16:
            yield t
        if t[0][1] != 16:
            yield ((16, 16),) + t
    escapement = solve(
            ratio=90,
            min_dx=0, max_dx=0,
            min_dy=-5, max_dy=-3,
            max_depth=6,
            spacing=1,
            err=0.05,
            train_mapper=_insert_clutchable)
    escapement_axles = (list(position_axles(a)) for a in escapement)
    glued = []
    for h in hands_axles:
        for e in escapement_axles:
            # glue the trains together
            middle = h[-1][0:1] + e[0][1:]
            # add the 40t escapement gear at the end of the escapement train
            end = e[-1][0:1] + (40,) + e[-1][2:]
            glued.append(h[:-1] + [middle] + e[1:-1] + [end])
    glued = list(map(layer_axles, glued))
    glued.sort(key=placement_score)
    return glued
```

And here's what some of the sample outputs look like:
```
9 axles 0 half-spaced in an (8.25, 10.25) area; gears: [8, 8, 8, 8, 12, 12, 16, 16, 20, 24, 24, 24, 36, 40, 40, 40] studs: (4, 7, 5) layers: 4
  layer 1:
                 (   0,   0,   0) 16
                 (  -2,   0,   0) 16
                 (  -3,  -2,   0) 20             (  -3,  -2,   1) 24
  layer 2:
                 (  -3,   0,   0) 40             (  -3,   0,   1)  8
                 (   0,   0,   0)  8             (   0,   0,   1) 24
  layer 3:
                 (  -2,   0,   0) 40             (  -2,   0,   1)  8
                 (  -2,  -3,   0)  8             (  -2,  -3,   1) 36
                 (  -2,  -6,   0) 24             (  -2,  -6,   1) 12
  layer 4:
                 (   0,  -5,   0) 12             (   0,  -5,   1) 40
9 axles 0 half-spaced in an (8.25, 10.25) area; gears: [8, 8, 8, 8, 12, 12, 16, 16, 20, 24, 24, 24, 36, 40, 40, 40] studs: (4, 7, 5) layers: 3
  layer 1:
                 (   0,   0,   0) 16
                 (  -2,   0,   0) 16
                 (  -3,  -2,   0) 20             (  -3,  -2,   1) 24
  layer 2:
                 (  -3,   0,   0) 40             (  -3,   0,   1)  8
                 (   0,   0,   0)  8             (   0,   0,   1) 36
                                                 (   0,  -3,   1) 12             (   0,  -3,   2) 40
                 (   0,  -6,   0) 24                                             (   0,  -6,   2)  8
                 (  -2,  -6,   0)  8             (  -2,  -6,   1) 24
  layer 3:
                 (   0,  -5,   0) 40             (   0,  -5,   1) 12
```
How to read the output:
* Each "layer" is a set of axles sandwiched between two planes of support, ex technic beams
* Each triplet is an (x,y) position + z-index within the current "layer"
* the number next to the triplet is the gear size (in teeth)
* Entries on the same line go on the same axle
* Following the snaking flow of power in the gear train from top to bottom tells you which gears are supposed to mesh together

It's a crude blueprint of how the gear train should be arranged so that gears don't overlap with each other or their axles. But its very useful compared to the other web tools -- thanks to this script, I was able to find some new arrangements and cram the clock mechanism into a smaller volume.

## How Does The Clock Work?

The drive chain is routed directly through the support structure.

![rear of the mechanism showing the drive train](/img/lego-clock/PXL_20221020_220632740.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The gear on the right connects to the gear train and powers the clock.
The gear on the left is used to wind the clock; the ratchet beneath the gear allows it to only turn counter-clockwise and lift the weight.

![ratcheting winding gear](/img/lego-clock/winding ratchet.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The [H-loop chain design](https://www.youtube.com/watch?v=C6U09_QZjZU) is great because it allows the clock to continue running almost unaffected while rewinding without any additional parts besides the chain loop itself.
The only real downside is that it requires roughly twice as many chain links compared to a regular loop.

Yanking on the chain to lift the weight and rewind the clock does put some additional tension on the drive gear.
Unfortunately grasshopper clocks don't allow the pendulum to swing entirely freely, so the pendulum frequency that is dependent on the drive force. In theory, while winding, some drive force is added and causes the clock to run faster.
In practice, I find that its negligable.

This is what the hour/minute hand gearing looks like without any of the supports:

![hour and minute hand gear train](/img/lego-clock/hands gear train.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The coaxial hour and minute hands have a 12x gear ratio between them (16:16, 16:20, 24:8, 40:8).
The minute hand is connected directly to the long central axle; the hour hand is a toothed 1x4 plate connected to the dark gray 16-tooth clutch gear.

The red 16-tooth gear is where the minute hand connected to the rest of the gear train; there is a friction in in the red gear axled to a 24-tooth gear where the train continues.
That friction pin is the key to allowing the hands to be set manually: it allows the weight to power the minute hand with small torques during normal operation, but slips when higher torques are applied by a human moving the minute hand.

Here's what the other side of the gear train looks without any supports:

![escapement drive train](/img/lego-clock/drive train.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The axle with the red gear is the connection to the minute hand.
The next axle in the train (the axle with the large black 36-tooth gear and two stacked 24-tooth gears) is the axle driven by the weights; the rightmost 24-tooth gear is where the drive chain goes.
The train snakes down and then back up to the green 40-tooth escapement wheel in a 90x[^5] ratio (36:12, 20:12, 24:12, 24:8, 24:8).

The pendulum has bionicle teeth that sit on a knife edge made of angled liftarms to minimize friction:

![knife edge pendulum mount](/img/lego-clock/knife edge.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

A regular axle rotating inside of (and supported by) an axle hole wastes too much energy to friction to be efficient.

The clock uses a [grasshopper escapement](https://en.wikipedia.org/wiki/Grasshopper_escapement) inspired by Dillon Sharlet's [grasshopper escapement](https://www.youtube.com/watch?v=To6Pg0Fo_8Q). The pendulum has some gray escapement pallets at the top for interfacing mechanically with the green escapement wheel:

![grasshopper escapement](/img/lego-clock/grasshopper escapement.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The escapement and pendulum cycle in steps:
  1. pallet A engages with the escapement teeth
  2. the escapement wheel pushes the engaged pallet A + pendulum, restoring some energy to the pendulum
  3. once the pendulum swings far enough, the other pallet B makes contact with the teeth
  4. the escapement wheel recoils slightly, releasing pallet A
  5. the escapement wheel pushes the newly engaged pallet B, and the cycle continues

Since the pallets are allowed to rotate while in contact with the escapement teeth, and are released by the wheel recoiling, the pallets never need to slide against the escapement wheel's teeth, greatly reducing friction and wear of the soft plastic Lego pieces.

Compare this to the [lever escapement](https://en.wikipedia.org/wiki/Lever_escapement) commonly found in modern wrist watches where the escapement tooth slides along the face of the pallets.
Dillon Sharlet has an [excellent video](https://www.youtube.com/watch?v=eM4AT5o_-Xc) documenting the wear after one year of operation for a similar escapement with sliding pallets.

The pallet positions and pallet stops can both be adjusted by sliding pieces along their supporting axles. The goals when tuning the escapement:
  * pallets should make contact with the escapement teeth when the pendulum is at the apex of its swing
  * both pallets should have equal 0.5s contact time with the wheel (ticks and tocks should be equal length)
  * contact angle should minimize the chance that the pallets slip

When the escapement is well-tuned, it "wants" to run and is moderately self-correcting.

Speaking of tuning, the pendulum length can be fine-tuned by turning a worm gear to shorten or lengthen it slightly:

![pendulum adjustment](/img/lego-clock/PXL_20221020_222812518.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

Finally, I added some rubber feet to the back of the clock to help support the body and allow it to be leveled along all 3 axes:

![rubber feet](/img/lego-clock/PXL_20221020_215251438.jpg){:style="display:block; margin-left:auto; margin-right:auto"}

The vertical beam on the back of the frame allows the clock to be hung from a single nail.

## Future Improvements

I'm pretty happy with the clock in its current form. But things can always be better. Some things I'd love to incorporate into future upgrades:
  * an enclosure that looks like a real wooden wall clock
  * an hourly chime
  * higher gear ratio and longer autonomy
  * a built-in on/off mechanism to halt or disengage the movement (in case of maintenance or migraines)

## Footnotes

[^1]: Admittedly my wrist watch may be overdue for servicing.
[^2]: Why a 40-tooth escapement wheel? More teeth is better in terms of efficiency, and despite my best efforts, none of my escapement prototypes using a 60-tooth turntable gear were successful due to its extra weight, rotational inertia, and increased friction.
[^3]: Why a 24-tooth drive gear? Smaller gears seem preferable since they are effectively the same as using a higher gear ratio in the drive train. 16-tooth gears would have also worked, but 8-tooth gears are too small to mesh smoothly with chain links.
[^4]: Any greedy algorithm won't be guaranteed to find the optimal arrangement by any metric. However I still found it useful -- it's more or less how I was trying to arrange gears myself.
[^5]: A 90x ratio between the drive gear and the escape wheel is slightly less than the required 108x, so a little extra drop distance is required to hit a full 24 hour autonomy. However, it is very space efficient since it doesn't require any additional axles or gear pairs.
