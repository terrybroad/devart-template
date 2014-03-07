So instead of diving straight into the pit of no return - that is, developing my own uber-sophisticated shape detection and tracking algorithms - I decided to side track a bit and try doing some fun things with texture mapping onto a spring  mesh physics simulation! 

My idea was to have each vertex on the mesh (onto which the video feed is mapped) to be connected to the adjacent vertices by springs. It seemed simple enough, but it still became a bit of a headache to do in the end. So I could have full control over what was going on, I wanted to avoid using a library like Box2d. I've pretty quickly come to the realisation that libraries aren't as instantly and magically useful as I had presumed, and are actually quite clunky and annoying. 

This meant having to knuckle down and wrap my head around some quite difficult equations. I started by dusting off some old A-Level physics books, and trying to figure out how I would translate this into a mesh of hundreds of constantly updated springs. 

![image3](../project_images/spring-notes.jpg)

I thought I had figured it out, but had yet to test out my hypothesis. I made a processing sketch to test it out before I jumped straight into the far less forgiving world of c++. Surprisingly I got it too work pretty quickly.

https://www.youtube.com/watch?v=FpnJLLciJkY

Confident that I had a reasonably accurate simulation of a springs behaviour, I implemented it in a modified version of the 'wonderland' blob detection code. And this is what I got:

https://www.youtube.com/watch?v=-S8ULznmxOc

All working surprisingly well, now for the money shot!

https://www.youtube.com/watch?v=TvpKUIxnjrY

And with more springs (This is a little slow as I'm screen recording):

https://www.youtube.com/watch?v=_uVZpjfzhlw

All in all I'm very happy with the rate of progress I've been making in the past week :)

##Link to prototype

[The code for this is on github](https://github.com/terrybroad/Spring-Mesh-Webcam)

##TO DO:

Re-make the spring simulation as a vertex shader, I think some lightning fast GPU parallel processing couldn't hurt.

Have the motion of the springs controlled by motion detection. 

Have the motion of the springs controlled by audio. 
