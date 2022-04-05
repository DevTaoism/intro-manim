Rate functions
===================

Rate functions are, as their name implies, functions, normalized functions, whose **domain** goes from 0 to 1 and the **range** must be within the interval :math:`[0,1]`, but nothing prevents it from starting or ending in those limits.

:math:`0` means :math:`0\%` of ``run_time`` and :math:`1` means :math:`100\%` of ``run_time``, and ``run_time`` is the total duration of the animation.

Some rate functions that are already predefined are explained below.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/RateFunctions.mp4" type="video/mp4">
    </video>
    </div>
    <br/>

The following animation explains how the **rate functions** work. Each animation starts from a state of :math:`0\%` complete to :math:`100\%` complete, the rate functions change the behavior of that increment.

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/PlotRateFunctions.mp4" type="video/mp4">
    </video>
    </div>
    <br/>

These functions are used to change the way class animations work.

Each class animation has a predefined rate function, for example, the ``Write`` animation has ``linear`` as the default ``rate_func``. But they are easy to change.

If we play around with the behavior of the function we can do something like this:

.. code-block:: python

    def construct(self):
        text = Text("Smooth")
        text.set(width=config.frame_width-1)
        
        self.play(
            Write(text,rate_func=smooth)
        )
        self.wait()
        self.play(
            # inverse smooth function
            Write(text,rate_func=lambda t: smooth(1-t))
        )
        self.wait()

.. raw:: html

    <div style="position: relative; padding-bottom: 56.25%; height: 0; overflow: hidden; max-width: 100%; height: auto;">
    <video allowfullscreen style="position: absolute; top: 0; left: 0; width: 100%; height: 100%;" controls>
        <source src="../_static/basic_videos/RateF1.mp4" type="video/mp4">
    </video>
    </div>
    <br/>

Inside the ``Scene.play`` method you can specify a ``rate_func`` and ``run_time`` for **all animations**, or you can specify each ``rate_func`` and ``run_time`` for each animation.

.. code-block:: python

    # For all Animations
    self.play(
        Animation1(...),
        Animation2(...),
        Animation3(...),
        ....
        AnimationN(...),
        rate_func=some_rate_func,
        run_time=some_run_time,
    )

    # For each animation
    self.play(
        Animation1(...,rate_func=rf1,run_time=rt1),
        Animation2(...,rate_func=rf2,run_time=rt2),
        Animation3(...,rate_func=rf3,run_time=rt3),
        ....
        AnimationN(...,rate_func=rfN,run_time=rtN),
    )

In short, ``rate_func`` is the **behavior** of the animation, and ``run_time`` is the **duration** of the animation. And they can be defined for each (class) animation or for all animations within a ``Scene.play``.