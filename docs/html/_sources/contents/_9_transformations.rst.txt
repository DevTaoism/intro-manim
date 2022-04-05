Transformations
========================

The transformations are classified into two:

* ``Transform``
* ``ReplacementTransform``

All other animations in this style are derived from these two.

Transform
---------------

To understand the transformations, we must first understand that each instance of a Mobject has a special identifier that differentiates it from the others, thus, two instances of the same Mobject will have different identifiers.

.. code-block:: python

    def construct(self):
        d1 = Dot()
        d2 = Dot()
        d3 = d1
        d4 = d2.copy()
        print(f"id(d1) = f{id(d1)}")
        print(f"id(d2) = f{id(d2)}")
        print(f"id(d3) = f{id(d3)}")
        print(f"id(d4) = f{id(d4)}")

Results (The values change on every computer or session):

.. code-block:: 

    id(d1) = f5547969504
    id(d2) = f5548828560
    id(d3) = f5547969504
    id(d4) = f5534488176

You can see that even though ``d1`` and ``d2`` are instances of the same ``Mobject`` (``Dot``) but they have different **id**. But, ``d1`` and ``d3`` have the same **id**, that means, that both values point to the same place in memory, so it is the same to modify ``d1`` or ``d3``, since it is exactly the same object, only with a different name. ``d4`` is an object created from ``d2``, but they are not the same object.

This is not something special from Manim, all objects are like that.

Something similar happens in transformations.

Transform **needs two and only two** ``Mobjects``, the **object to transform** and the **target**.
Transform copies the **properties** of the **target** and passes them to the **object to be transformed**, but does not modify the target, it only modifies the **object to transform**.

.. code-block:: python

    def construct(self):
        obj = Text("X")
        t_a = Text("A")
        t_b = Text("B")
        t_c = Text("C")
        t_d = Text("D")

        self.add(obj)
        self.play(Transform(obj,t_a))
        self.play(Transform(obj,t_b))
        self.play(Transform(obj,t_c))
        self.play(Transform(obj,t_d))
        self.wait()
        t_grp = VGroup(t_a,t_b,t_c,t_d)\
            .arrange(DOWN)\
            .shift(RIGHT)
        self.play(Write(t_grp))
        self.wait()

It is easy to notice here that neither ``t_a``, ``t_b``, ``t_c`` nor ``t_d`` changed their value, the only Mobject that changed was ``obj``.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/tr1.mp4" type="video/mp4">
    </video>
    </div>
    <br/>

ReplacementTransform
--------------------------

It is equivalent to Transform, with the detail that ReplacementTransform does change the content of the target.

.. code-block:: python

    def construct(self):
        obj = Text("X")
        t_a = Text("A")
        t_b = Text("B")
        t_c = Text("C")
        t_d = Text("D")

        self.add(obj)
        self.play(ReplacementTransform(obj,t_a))
        # self.play(ReplacementTransform(obj,t_b)) # <- This not works
        self.play(ReplacementTransform(t_a,t_b))
        self.play(ReplacementTransform(t_b,t_c))
        self.play(ReplacementTransform(t_c,t_d))
        self.wait()
        t_grp = VGroup(obj,t_a,t_b,t_c)\
            .arrange(DOWN)\
            .shift(RIGHT)
        self.play(Write(t_grp))
        self.wait()

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/tr2.mp4" type="video/mp4">
    </video>
    </div>
    <br/>

You can notice that, while in ``Transform`` we always use the same object in the first argument, ``ReplacementTransform`` does change its first argument.

Transform vs ReplacementTransform
------------------------------------

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/TransformExplanation.mp4" type="video/mp4">
    </video>
    </div>
    <br/>

FadeTransform
--------------------

It is equivalent to Transform, only that instead of interpolating bézier curves, it uses a ``FadeIn`` and ``FadeOut`` to the Mobjects to transform:

.. code-block:: python

    def construct(self):
        r = Rectangle()
        c = Circle()
        VGroup(r,c).arrange(RIGHT)
        self.add(r,c)

        self.play(
            FadeTransform(r,c)
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs1.mp4" type="video/mp4">
    </video>
   </details>
   <br/>


This animation is especially useful when we transform formulas or text that does not have the same style. For example, if we use transform in this animation:

.. code-block:: python

    def construct(self):
        t1 = MathTex("e^","\\frac{-it\\pi}{\\omega}")
        t2 = MathTex("\\frac{-it\\pi}{\\omega}")
        VGroup(t1,t2)\
            .scale(3)\
            .arrange(DOWN,buff=2)
            
        self.add(t1,t2.copy().fade(0.8))
        self.wait(0.3)
        self.play(
            ReplacementTransform(t1[-1].copy(),t2[0]),
            run_time=6
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs2.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

You can see that the transformation does not look good, this is because, although it is the same text, the letters have slightly different sizes and shapes, for this we can use ``FadeTransform``, in this case we will use ``FadeTransformPieces``, which allows us to transform each **submobject** (Mobjects inside other Mobjects) using ``FadeTransform``. That is, ``FadeTransformPieces`` will transform each letter using ``FadeTransform``. If we used ``FadeTransform`` then it would apply to the entire formula.

.. code-block:: python

    def construct(self):
        t1 = MathTex("e^","\\frac{-it\\pi}{\\omega}")
        t2 = MathTex("\\frac{-it\\pi}{\\omega}")
        VGroup(t1,t2)\
            .scale(3)\
            .arrange(DOWN,buff=2)
            
        self.add(t1,t2.copy().fade(0.8))
        self.wait(0.3)
        self.play(
            FadeTransformPieces(t1[-1].copy(),t2[0]),
            run_time=4
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs3.mp4" type="video/mp4">
    </video>
   </details>
   <br/>


TransformMatchingShapes
---------------------------

This animation try to transform groups by matching the shape of their submobjects.

Two submobjects match if the hash of their point coordinates after normalization (i.e., after translation to the origin, fixing the submobject height at 1 unit, and rounding the coordinates to three decimal places) matches.

.. code-block:: python

    def construct(self):
        from random import shuffle
        def get_mobs():
            mob = [Square(),Circle(),Triangle(),Text("Hello")]
            shuffle(mob)
            return mob

        grp1 = VGroup(*get_mobs()).arrange(DOWN)
        grp2 = VGroup(*get_mobs()).arrange(DOWN)

        VGroup(grp1,grp2).arrange(RIGHT,buff=4)

        self.add(grp1,grp2)

        self.play(
            TransformMatchingShapes(
                grp1.copy(), grp2
            )
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs4.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Other example:

.. code-block:: python

    def construct(self):
        source = Tex("the morse code", height=1)
        target = Tex("here come dots", height=1)

        self.add(source)
        self.wait()
        kw = {"run_time": 3, "path_arc": PI / 2}
        self.play(TransformMatchingShapes(source, target, **kw))
        self.wait()
        self.play(TransformMatchingShapes(target, source, **kw))
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs5.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

TransformMatchingTex
------------------------

It is equivalent to the previous transformation, and what it does is transform each ``tex_string``. It is recommended to separate each tex_string that you want to isolate for the transformation, there are two ways, using an array with the strings to isolate.

.. code-block:: python

    def construct(self):
        isolate_tex = ["x","y","3","="]
        t1 = MathTex("x+y=3",substrings_to_isolate=isolate_tex)
        t2 = MathTex("x=3-y",substrings_to_isolate=isolate_tex)
        VGroup(t1,t2)\
            .scale(3)
        t2.align_to(t1,LEFT)

        self.add(t1)
        self.wait()
        self.play(
            TransformMatchingTex(
                t1,t2,
                # Try removing this dict
                key_map={
                    "+":"-"
                }
            ),
            run_time=4
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs6.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Or using this format:

.. code-block:: python

    def construct(self):
        t1 = MathTex("{{x}}+{{y}}={{4}}")
        t2 = MathTex("{{x}}={{4}}-{{y}}")
        VGroup(t1,t2)\
            .scale(3)
        t2.align_to(t1,LEFT)

        self.add(t1)
        self.wait()
        self.play(
            TransformMatchingTex(
                t1,t2,
                # Try removing this dictionary
                key_map={
                    "+":"-"
                }
            ),
            run_time=4
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs7.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Use the key_map dictionary to specify symbols that you want to transform into others.

In general, this transformation works well for simple formulas, but for more complex formulas we will need to use indexes.

This fail with a little complex formulas (roots, fractions, etc):

.. code-block:: python

    def construct(self):
        isolate_tex = ["a","b","c","="]
        t1 = MathTex("a \\times b = c",substrings_to_isolate=isolate_tex)
        t2 = MathTex("a = { c \\over b }",substrings_to_isolate=isolate_tex)
        VGroup(t1,t2)\
            .scale(3)
        t2.align_to(t1,LEFT)

        self.add(t1)
        self.wait()
        self.play(
            TransformMatchingTex(
                t1,t2,
                # This not works
                key_map={
                    "\\times":"\\over"
                }
            ),
            run_time=4
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs8.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Transform using indexes
----------------------------

Sometimes the formulas we want to transform are very complex, so ``TransformMatchingShapes`` or ``TransformMatchingTex`` won't work, so the only alternative is to use ``Transform`` with **subindexes**.

For this, we need to identify each index of each formula, we can do that using Manim itself.

Using this function:

.. code-block:: python

    def get_sub_indexes(tex):
        ni = VGroup()
        colors = cycle([RED,TEAL,GREEN,BLUE,PURPLE])
        for i in range(len(tex)):
            n = Text(f"{i}",color=next(colors)).scale(0.7)
            n.next_to(tex[i],DOWN,buff=0.01)
            ni.add(n)
        return ni

We can identify the subindexes of each formular, for example:

.. code-block:: python

    def construct(self):
        #                           Why this?    |
        #                                        v
        source = MathTex("\\sqrt{\\frac{1}{8}}")[0]
        target = MathTex("\\frac{1}{2\\sqrt{2}}")[0]
        # If you ask yourself this, go back to "Tex as array"
        # section in the "Text and Tex" chapter

        VGroup(source,target).scale(4).arrange(RIGHT,buff=2)
        source_ind = get_sub_indexes(source)
        target_ind = get_sub_indexes(target)

        self.add(
            source, source_ind,
            target, target_ind
        )

.. image:: ../_static/images/subindexes.png

Now all we have to do is relate them.

.. image:: ../_static/images/subindexes2.png

.. code-block:: 

    0 [root v]    --> 3 [root v]
    1 [root top]  --> 4 [root top]
    2 [1]         --> 0 [1]
    3 [fraq line] --> 1 [fraq line]
    4 [8]         --> 2 [2 left], 5 [2 right]

We can do it in many ways, one of those could be:

.. code-block:: python

    def construct(self):
        source = MathTex("\\sqrt{\\frac{1}{8}}")[0]
        target = MathTex("\\frac{1}{2\\sqrt{2}}")[0]

        VGroup(source,target).scale(4)
        self.add(source)
        transform_index = [
            [0,1,2,3,4,4],
           # | | | | | | <   Note that we repeat the index 4 twice,
           # v v v v v v     since the "8" is going to transform 
            [3,4,0,1,2,5] #  into two different symbols.
        ]
        self.play(
            *[
                ReplacementTransform(source[i],target[j])
                for i,j in zip(*transform_index)
            ]
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs9.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

We notice that the 8 does not transform well, because it is transforming into 2 different symbols, what we can do is duplicate it using a copy, a simple way would be:

.. code-block:: python

    def construct(self):
        source = MathTex("\\sqrt{\\frac{1}{8}}")[0]
        target = MathTex("\\frac{1}{2\\sqrt{2}}")[0]

        VGroup(source,target).scale(4)
        self.add(source)
        transform_index = [
            [0,1,2,3,4,"r4"],
           # | | | | |  |
           # v v v v v  v
            [3,4,0,1,2, 5]
        ]
        self.play(
            *[
                # Try replacing "ReplacementTransform" with "FadeTransform"
                ReplacementTransform(source[i],target[j])
                if type(i) is int else 
                ReplacementTransform(source[int(i[1:])].copy(),target[j])
                for i,j in zip(*transform_index)
            ],
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs10.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

Following the same example we can continue adding conditionals to do things like this:

.. code-block:: python

    def construct(self):
        source = MathTex("\\sqrt{\\frac{1}{8}}")[0]
        target = MathTex("\\frac{1}{2\\sqrt{2}}")[0]

        VGroup(source,target).scale(4)
        self.add(source)
        transform_index = [
            ["f0","f1",2,3,4,"r4"],
           #  |    |   | | |  | 
           #  v    v   v v v  v 
            [ 3,   4,  0,1,2, 5]
        ]
        self.play(
            *[
                ReplacementTransform(source[i],target[j])
                if type(i) is int else 
                ReplacementTransform(source[int(i[1:])].copy(),target[j])
                if i[0]=="r" else
                FadeTransform(source[int(i[1:])],target[j])
                for i,j in zip(*transform_index)
            ],
            run_time=3
        )
        self.wait()

.. raw:: html

   <details>
   <summary> Show result </summary>
    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/trs11.mp4" type="video/mp4">
    </video>
   </details>
   <br/>

In a later workshop we will teach a method to make this process simpler.