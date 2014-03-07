For the time being, while I wait for my new webcams to arrive, I've been working on trying to make some interesting algorithms for just distorting a single webcam feed. 

An idea for a visual effect that manipulates someones vision would be to track shapes and objects in the scene and warp the image, manipulating the size and shape of the images. Making some shapes larger and some smaller, almost like an 'Alice In Wonderland' effect. 

http://www.youtube.com/watch?v=P4sywpg-nrY

This is my first attempt at doing something like that. I'm using the ofxOpenCV addon with openframeworks and using the Contour finder algorithm to detect blobs in the scene. Then I am finding the center point of each blob and using that to manipulate the position of the verticies in the mesh of triangles that the video image is texture-wrapped onto. 

https://www.youtube.com/watch?v=qPbNTcucNdM

These videos manipulate the points inwards and outwards.

https://www.youtube.com/watch?v=WhknH0x3N54

##Link to prototype
[The code for this is on github](https://github.com/terrybroad/wonderland_webcam)

Its not very well commented and a bit messy as it is a work in progress. 

##TO DO:

Change the way it finds contours: at the moment it only takes a black and white threshold and uses that to find the blobs, this is certainly not a very good method, probably need to implement a edge detection kernel filter. 

Need to make it smoother either by averaging the positions of the vertex points of the meshes from, say, the last 3 frames, or by trying to track the blobs instead of them being completely discarded and made fresh each frame. 

Need to manipulate the points in a way that is relative to the size and shape of the blob. To do this I will probably have to implement my own blob class and contourfinding class... lots of fun ahead.
