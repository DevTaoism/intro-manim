Methods as animations
===========================

Once we have understood how the transformations work, we can enter other functionalities based on them. Everything that we will see next is derived from the transformations.

MoveToTarget
----------------

This is quite a useful animation.
Basically it is about creating a temporary copy of a Mobject called target, then making modifications to that copy (target) to end with a transformation from the original Mobject to the target.

We could do this manually, in the following example we will see MoveToTarget on the left side and on the right side we will see its analog manually.

.. code-block:: python

    def construct(self):
        source_left  = Dot()
        source_right = source_left.copy()

        VGroup(source_left,source_right).arrange(RIGHT,buff=3)

        # Left side - MoveToTarget ----------------
        source_left.generate_target()
        # Manupulate the .target attr
        source_left.target.set_style(
            fill_color=TEAL,
            stroke_width=10,
            stroke_color=ORANGE
        )
        source_left.target.scale(7)
        source_left.target.to_edge(UP)
        # Right side - Manually ----------------
        source_right_target = source_right.copy()
        source_right_target.set_style(
            fill_color=TEAL,
            stroke_width=10,
            stroke_color=ORANGE
        )
        source_right_target.scale(7)
        source_right_target.to_edge(UP)

        # Animations
        self.add(source_left,source_right)
        self.play(
            MoveToTarget(source_left),
            Transform(source_right,source_right_target),
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at1.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

It may not sound like a big deal, and it certainly isn't, but using this technique saves you from creating new variables that you have to remember the name of. It is a good idea to use it on certain occasions.

ApplyFunction
----------------

This is another very useful animation, basically it allows you to enter a function that is going to be applied to some Mobject.

.. code-block:: python

    def construct(self):
        source  = Dot()

        def custom_func(mob):
            mob.set_style(
                fill_color=TEAL,
                stroke_width=10,
                stroke_color=ORANGE
            )
            mob.scale(7)
            mob.to_edge(UP)
            # Don't forget return mob
            return mob

        self.add(source)

        self.play(
            ApplyFunction(custom_func,source),
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at2.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

But not only that, this can be applied to several Mobjects at the same time, making this animation reusable:

.. code-block:: python

    def construct(self):
        source  = VGroup(Dot(),Square(),Circle(),Text("A"))\
            .arrange(RIGHT,buff=2)

        def custom_func(mob):
            mob.set_style(
                fill_color=TEAL,
                stroke_width=10,
                stroke_color=ORANGE
            )
            mob.scale(3)
            mob.to_edge(UP)
            # Don't forget return mob
            return mob

        self.add(source)

        self.play(
            *[
                ApplyFunction(custom_func, mob)
                for mob in source
            ],
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at3.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Wait, there is more, using the concept of **closures**, we can do more interesting things, instead of having a static function we can create a function that generates other functions, and reuse it even more flexibly.

In Manim, these types of closures are often created as separate methods from construct.

.. code-block:: python

    class MyScene(Scene):
        def construct(self):
            source  = VGroup(Dot(),Square(),Circle(),Text("A"))\
                .arrange(RIGHT,buff=2)

            self.add(source)

            self.play(
                ApplyFunction(self.custom_method(scale=7,edge=DOWN), source[0]), # Dot
                ApplyFunction(self.custom_method(fill_color=PURPLE), source[1]), # Square
                ApplyFunction(self.custom_method(fill_opacity=0),    source[2]), # Circle
                ApplyFunction(self.custom_method(edge=LEFT),         source[3]), # Text("A")
                run_time=3
            )
            self.wait()

        def custom_method(self,
                        fill_color=TEAL,
                        fill_opacity=1,
                        stroke_width=10,
                        stroke_color=ORANGE,
                        scale=3,
                        edge=UP,
                    ):
            def custom_func(mob):
                mob.set_style(
                    fill_color=fill_color,
                    fill_opacity=fill_opacity,
                    stroke_width=stroke_width,
                    stroke_color=stroke_color,
                )
                mob.scale(scale)
                mob.to_edge(edge)
                # Don't forget return mob
                return mob
            # Don't forget return the func
            return custom_func

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at4.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Another functionality of ApplyFunction is the manipulation of VGroups or Groups, since it allows manipulating each element of the VGroups/Groups if we need it.

.. code-block:: python

    def construct(self):
        source  = VGroup(Dot(),Square(),Circle(),Text("A"))\
            .arrange(DOWN)

        def grp_func(vgrp):
            vgrp.scale(2)
            d,s,*other = vgrp
            # The order is important
            d.scale(4)
            s.set_color(PURPLE)
            vgrp.arrange(RIGHT,buff=0)
            VGroup(*other).set_color(TEAL)
            return vgrp

        self.add(source)

        self.play(
            ApplyFunction(grp_func, source),
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at5.mp4" type="video/mp4">
    </video>
   </details>
   <br/>


``.animate``
----------------

This functionality is possibly the most versatile. It basically allows us to animate almost any **interpolatable** method, and its use is quite simple:

.. code-block:: python

    def construct(self):
        source  = VGroup(Dot(),Square(),Circle(),Tex("Hello ","World","!"))\
            .arrange(RIGHT,buff=1).to_edge(DOWN)

        d,s,c,t = source

        self.add(source)

        self.play(
            # Single method
            d.animate.to_edge(UP),
            # Multi-methods single line
            s.animate.shift(UP*2).scale(1/2).set_color(PURPLE),
            # Multi-methods multi lime
            c.animate
                .set_x(-1)
                .scale(1/2)
                .set_style(fill_opacity=1,fill_color=PINK,stroke_color=WHITE),
            # Even VGroups or Groups
            t.animate.arrange(DOWN,aligned_edge=LEFT).move_to(t.get_center()+UP*3),
            #                                        -----------------------------
            #       Remember that the arrange method moves the Mobjects to the center
            #       so we can't use "shift" method here.
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at6.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

We can think of ``.animate`` as a quick equivalent to ``MoveToTarget`` or ``ApplyFunction``:

.. code-block:: python

    def construct(self):
        vgrp = VGroup(Square(),Square(),Square())\
            .arrange(DOWN).to_edge(LEFT)
        s1, s2, s3 = vgrp
        SCALE = 0.3

        s2.generate_target()
        s2.target.scale(SCALE)\
                .set_fill(opacity=1)\
                .set_color(TEAL)\
                .to_edge(RIGHT)

        self.add(vgrp)

        self.play(
            # .animate  ----------------------------
            s1.animate
                .scale(SCALE)
                .set_fill(opacity=1)
                .set_color(TEAL)
                .to_edge(RIGHT),
            # MoveToTarget  ------------------------
            MoveToTarget(s2),
            # ApplyFunction  -----------------------
            ApplyFunction(
                lambda mob: mob.scale(SCALE)
                    .set_fill(opacity=1)
                    .set_color(TEAL)
                    .to_edge(RIGHT),
                s3
            ),
            run_time=2
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at6_2.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Special cases
----------------

There are some animations that come by default and allow us to color, scale or delete an object.
The limitation of this is that you cannot use two class animations at the same ``Scene.play``, unlike ``.animate`` or the previous classes that allow us to make several transformations to the same object.

.. code-block:: python

    def construct(self):
        anims = [FadeToColor,ScaleInPlace,ShrinkToCenter]
        mobs = VGroup(*[Text(cls.__name__) for cls in anims])\
            .arrange(RIGHT,buff=1)

        self.add(mobs)
        self.play(
            FadeToColor(mobs[0],RED),
            ScaleInPlace(mobs[1],2),
            ShrinkToCenter(mobs[2])
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at7.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

However, there is an animation that is especially complex and that you cannot fully use with transformations, this is: Rotation.

Rotation
-------------

Rotation using methods:

.. code-block:: python

    def construct(self):
        angles = [10,30,60,90,120]
        mobs = VGroup(*[
            VGroup(MathTex(f"{angle}^\\circ"),Square())
                .arrange(DOWN,buff=1)
            for angle in angles
        ]).arrange(RIGHT,buff=0.7)

        self.add(mobs)
        self.play(
            *[
                # mob[0] is the MathTex and
                # mob[1] is the Square()
                mob[1].animate.rotate(angle*PI/180)
                for mob,angle in zip(mobs,angles)
            ],
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at8.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

You can see that the greater the angle, the more deformed the rotation.
This is because, what happens in reality, is that the squares are transformed to another square already rotated, that is, the rotations are not continuous, but it is a simple transformation between two states.
To solve this we can use ``Rotating``:

.. code-block:: python

    def construct(self):
        angles = [10,30,60,90,120]
        mobs = VGroup(*[
            VGroup(MathTex(f"{angle}^\\circ"),Square())
                .arrange(DOWN,buff=1)
            for angle in angles
        ]).arrange(RIGHT,buff=0.7)

        self.add(mobs)
        self.play(
            *[
                # mob[0] is the MathTex and
                # mob[1] is the Square()
                Rotating(mob[1],radians=angle*PI/180)
                for mob,angle in zip(mobs,angles)
            ],
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/at9.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

.. note::

    Instead of writing ``PI/180``, ``DEGREE`` is usually used, which is exactly the same.

How this animation is done is more complex, we will understand it when we see **alpha-type updaters**.