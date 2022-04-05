.. role:: underbold
    :class: underbold

Basic Camera and render settings
====================================

As we explained in previous chapters, the camera has an aspect ratio of 16/9, it has a black background and there are several default rendering options (``l``, ``m``, ``h`` and ``k``). In this chapter we are going to extend this knowledge.

There are three ways to configure the camera and rendering options:

* Modifying the ``config`` dictionary inside a .py script.
* Using the ManimCE CLI.
* Using a manim.cfg file.

manim.cfg
-------------

manim.cfg files do not always work very well, some properties work and others do not, at the moment I do not recommend using it. But if you want to learn how to use it, just type the command:

.. code:: bash

    manim cfg write -o

This command will create a configuration file with all the options that you can modify. As I said before, some options may not work, it is still under development.

Read the documentation `here <https://docs.manim.community/en/stable/tutorials/configuration.html#the-config-files>`_.

ManimCE CLI
---------------

Unfortunately the CLI is constantly changing, some functionalities may change as the versions progress, so I will leave the options that are more stable.


* :underbold:`PREVIEW`: ``-p``: Automatically open your file at the end of the rendering is done, be it a video or an image.
* :underbold:`QUALITY`: ``-ql``: 420p at 15FPS, ``-qm``: 720p at 30FPS, ``-qh``: 1080p at 60FPS, ``-qp``: 1440p at 60FPS and ``-qk``: 4K at 60FPS. 
* :underbold:`RENDER LAST FRAME AS IMAGE`: ``-s``.
* :underbold:`RENDER AS GIF`: ``--format=gif``.
* :underbold:`RENDER AS MOV`: ``--format=mov`` - This format is the one with the best quality.
* :underbold:`SET FPS`:  ``--fps=SOME_NUMBER``.
* :underbold:`WITH TRANSPARENCY`: ``-t`` - If you use this flag while rendering a video then it will be exported in **.mov** format in an alpha channel, in case you render an image it will export a **PNG** without background.
* :underbold:`CHANGING ASPECT RATIO`: ``-r W,H`` - For example: ``-r 1000,500``. **I do not recommend doing this unless you have a more advanced understanding of the camera**.
* :underbold:`CHANGE OUTPUT FILENAME`: ``-o FILE_NAME``.
* :underbold:`START RENDERING FROM...`: ``-n START,END`` - It only works in video rendering, it starts rendering from ``START`` animation to ``END`` animation, where ``START`` and ``END`` are the numbers of some animations, ``START`` < ``END``.
* :underbold:`RENDER ALL SCENES FROM FILE`: ``-a`` - If you have more than one class that inherits from Scene (or its variants) then it will render all these scenes.

Examples:
"""""""""""""""

1. Render a scene at 720p, no preview, at 15 fps as a gif and with the output name of MY_SCENE:

.. code:: bash

    manim scripy.py SceneName -qm --fps=15 --format=gif -o MY_SCENE

2. Render a scene with 800 pixels width x 700 pixels HEIGHT, at 20 fps, output name = OTHER_SCENE, starting at animation 3 and ending at animation 20, with preveiw and transparency.

.. code:: bash

    manim scripy.py SceneName -p --fps=20 -r 800,700 -t -o OTHER_SCENE -n 3,20

``config`` dict
-----------------

We can define almost all the options in our same **.py** script that we are working on without having to alter the CLI or use .cfg files.

.. code:: python

    from manim import *

    config.background_color = RED
    config.frame_rate = 25
    config.frame_size = [1200,500] # [WIDTH, HEIGHT]
    # config.transparent = True

    # RENDER AS IMAGE: ----------------
    config.format = "png"
    config.save_last_frame = True
    # config.transparent = True

    # REDER AS GIF: -------------------
    config.format = "gif"

    class SomeScene(Scene):
    ...

Configuration priority
-------------------------

Now, in case you want to use several of these settings at the same time, you should know that Manim is going to take the following priority:

1. ``config`` dict.
2. ManimCE CLI.
3. manim.cfg

That is, the configuration with the highest priority will be the ``config`` dictionary within your script, if you have not modified it then it will take the CLI, and if you have not configured the CLI then it will read your manim.cfg, and in case of If you haven't configured manim.cfg then Manim will use the default settings.