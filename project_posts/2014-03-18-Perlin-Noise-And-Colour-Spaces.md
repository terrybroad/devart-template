While i'm still working on building the second version of the camera rig, and calibrating the shaders to correct for the distortion of the webcam
lenses (which is dragging on a lot). I've been keeping myself sane by messing around with a new effect. Which using a perlin noise to change the
offset of the texture coordinates, and having this manipulated by music.

Here is the first video, just changing the offset for the texture coordinate of the full RGBA colour vector.
http://www.youtube.com/watch?v=fIyLTT4gg1s
This is doing the same thing but this time changing the offset of the red, green and blue channels separately.
http://www.youtube.com/watch?v=iyl-pukqIjM
And this is doing something similar but a bit more complicated, this is splitting it into three but this time sampling in the [CIE
XYZ]("http://en.wikipedia.org/wiki/CIE_1931_color_space") colour space, which is a device independent colour space, and can represent all colours that
can possibly exist (unlike sRGB). I wrote my own conversion algorithm in GLSL, here is the code:

And here is the video
http://www.youtube.com/watch?v=Ut7bkhviGAM&feature=youtu.be
I also attempted doing the same thing in the HSV colour space, and the [CIE LAB]("http://en.wikipedia.org/wiki/Lab_color_space") colour space (a
colour space where numerically equivalent changes correspond to perceptually equivalent changes in colour). However for this type of colour offsetting
effect the outcome was not particularly interesting (very little difference from the first video). I imagine this is because the brightness of a
colour is represented by one value in both of these colour spaces.

However It will be really useful going forward to being able to manipulate colours in these colour spaces, especially the LAB colour space as it is so
strongly related to human perception.


##Link to prototype

[The code for this is on github](https://github.com/terrybroad/Music_Re-Visualiser)
