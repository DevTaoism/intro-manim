.. role:: underbold
    :class: underbold

Basic Updaters 
======================

Basic theory
-----------------

Updaters are **functions** that are applied to a Mobject and that are updated every frame.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; 
        overflow: hidden; max-width: 100%; height: auto;
        border-width: 4px; border-style: solid; border-color: red;
    ">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/Updaters1.mp4" type="video/mp4">
    </video>
   </div>
   <br/>

:underbold:`Question`: How would we make sure that the square is always above the circle without having to manipulate the square?

Basically, we need the ``square.next_to(circle,UP)`` to apply each frame of the animation, for this we can use the Updaters.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; 
        overflow: hidden; max-width: 100%; height: auto;
        border-width: 4px; border-style: solid; border-color: red;
    ">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/Updaters2.mp4" type="video/mp4">
    </video>
   </div>
   <br/>

If the functions are simple then we can use anonymous functions to make it shorter.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; 
        overflow: hidden; max-width: 100%; height: auto;
        border-width: 4px; border-style: solid; border-color: red;
    ">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/Updaters3.mp4" type="video/mp4">
    </video>
   </div>
   <br/>

And this updater is going to run until we pause and delete it.

* Pause updaters: ``Mobject.suspend_updating()``
* Restore updaters: ``Mobject.resume_updating()``
* Delete all updaters: ``Mobject.clear_updaters()``

Yes, you can add more updaters, each Mobject has an attribute (a list) where it stores its updaters, so you can add or remove more updaters.

``.become``
----------------

Now imagine that we want the ``Brace`` to change in size depending on the line.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; 
        overflow: hidden; max-width: 100%; height: auto;
        border-width: 4px; border-style: solid; border-color: red;
    ">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/TryBrace.mp4" type="video/mp4">
    </video>
   </div>
   <br/>

There are some Mobjects that you can change their shape through their methods, but the most general way is to use ``.become``.

The easiest way to understand ``.become`` is by making the analogy with ``Transform``, basically ``.become`` is an instant ``Transform``, and I can demonstrate it with the following code.

.. code-block:: python

    def construct(self):
        c = Circle().scale(2)
        s = Square().scale(2)
        c.generate_target()
        c.target.become(s) # <-- it's an instant transformation

        self.add(c)
        self.play(MoveToTarget(c))
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/become.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Using this concept, we can instantly transform a Mobject each frame, so it will give the feeling that the transformation is smooth.

.. code-block:: python

    def construct(self):
        line = Line(LEFT,RIGHT)

        def get_brace(mob):
            mob.become(
                Brace(line,UP)
            )

        brace = VMobject() # This is the initial status, 
                           # when we add the updater it will update

        brace.add_updater(get_brace)

        self.add(line,brace)
        self.wait(0.5)
        self.play(line.animate.scale(3))
        self.wait(0.5)
        self.play(line.animate.scale(0.8))
        self.wait(0.5)
        self.play(line.animate.scale(1.3))
        self.wait(0.5)

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/become2.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Some Mobjects already have methods that help to deform them, for example, lines and vectors have the ``put_start_and_end_on`` method:

.. code-block:: python

    def construct(self):
        start_dot = Dot(LEFT,color=RED)
        end_dot   = Dot(RIGHT,color=TEAL)

        def update_line(mob):
            mob.put_start_and_end_on(start_dot.get_center(),end_dot.get_center())
        
        line = Line() # Also it works with Arrow
        line.add_updater(update_line)

        self.add(line, start_dot, end_dot)
        self.wait(0.5)
        self.play(start_dot.animate.shift(LEFT*2+UP))
        self.wait(0.5)
        self.play(end_dot.animate.shift(RIGHT+DOWN*0.5))
        self.wait(0.5)
        self.play(
            start_dot.animate.shift(RIGHT*0.5+DOWN*2),
            end_dot.animate.shift(RIGHT+UP*3).scale(5)
        )
        self.wait(0.5)
        self.play(
            start_dot.animate.shift(LEFT*3).scale(5),
            end_dot.animate.shift(DOWN*2)
        )
        self.wait(0.5)

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/upd1.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

ValueTracker
-----------------

This class does not appear on the screen, it is simply a tool that allows us to increase or decrease numerical values.

.. code-block:: python

    def construct(self):
        nl = NumberLine(include_numbers=True)
        selector = Triangle(fill_opacity=1).scale(0.2).rotate(PI/3)

        vt = ValueTracker(0) # <-- needs a start value

        def update_selector(mob):
            mob.next_to(nl.n2p(vt.get_value()),UP,buff=0)
            #                  --------------
            #  In this way we can acces to the ValueTracker value
        
        selector.add_updater(update_selector)

        self.add(nl,selector)
        self.wait(0.5)
        self.play(
            vt.animate.set_value(4),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            vt.animate.set_value(-1),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            vt.animate.set_value(-7),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            vt.animate.set_value(7),
            run_time=3
        )
        self.wait(0.5)

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/vt1.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

As an exercise try to duplicate this animation:

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/ex1.mp4" type="video/mp4">
    </video>
    </div>
   <br/>

.. raw:: html

   <details>
   <summary> Solution </summary>
    <a href="https://docs.manim.community/en/stable/examples.html#movingangle"> Official documentation </a>
   </details>
   <br/>
   <br/>

DecimalNumber
-----------------

Now we will understand the value of ``DecimalNumber``, which is similar to ``ValueTracker``, only that it can be displayed on the screen:

.. code-block:: python

    def construct(self):
        nl = NumberLine(include_numbers=True)
        selector = Triangle(fill_opacity=1).scale(0.2).rotate(PI/3)

        dn = DecimalNumber(0) # <-- needs a start value

        def update_selector(mob):
            mob.next_to(nl.n2p(dn.get_value()),UP,buff=0)
            #                  --------------
            #  In this way we can acces to the DecimalNumber value

        def update_dn(mob):
            mob.next_to(selector,UP,buff=0.1)
        
        selector.add_updater(update_selector)
        dn.add_updater(update_dn)

        self.add(nl,selector,dn)
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, 4),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, -1),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, -4),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, 7),
            run_time=3
        )
        self.wait(0.5)

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/dn1.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Group Updaters
-----------------

Sometimes it is easier for us to create an updater that modifies several Mobjects, this can be done using Groups. 

.. code-block:: python

    def construct(self):
        nl = NumberLine(include_numbers=True)
        selector = Triangle(fill_opacity=1,color=RED).scale(0.2).rotate(PI/3)
        dn = DecimalNumber(0) 

        update_grp = VGroup(selector, dn)

        def update_vgrp(vgrp):
            s,d = vgrp
            s.next_to(nl.n2p(dn.get_value()),UP,buff=0)
            d.next_to(selector,UP,buff=0.1)
        
        update_grp.add_updater(update_vgrp)

        self.add(nl,update_grp) # <-- add grp complete, not each element
        # Not use self.add(nl, selecttor, dn)
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, 4),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, -1),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, -4),
            run_time=2
        )
        self.wait(0.5)
        self.play(
            ChangeDecimalToValue(dn, 7),
            run_time=3
        )
        self.wait(0.5)

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/update_grp.mp4" type="video/mp4">
    </video>
   </details>
   <br/>