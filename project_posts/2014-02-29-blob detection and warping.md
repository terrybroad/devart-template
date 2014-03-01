So while I wait for my new webcams to arrive I've been working on trying to make some interesting algorithms for just distorting a single webcam feed for the time being. 

One of the idea's I for a visual effect in which to manipulate someones vision would be to track shapes and objects in the scene and warp the image, manipulating the size and shape of the images. Making some shapes larger and some smaller, almost like an 'Alice In Wonderland' kind of effect. 

[video](https://www.youtube.com/watch?v=1BbDZTKxXGQ)

This was my first attempt at doing something like that, Im using the ofxOpenCV addon with openframeworks and using the Contour finder algorithm to detect blobs in the scene. The I am finding the center point of each blob and using that to manipulate the position of the verticies in the mesh of triangles that the video image is texture-wrapped onto. 

[video](https://www.youtube.com/watch?v=qPbNTcucNdM)

These videos manipulate the points inwards and outwards.
