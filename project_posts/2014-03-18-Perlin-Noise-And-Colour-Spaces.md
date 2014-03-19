While I'm working on building the second version of the camera rig and calibrating shaders to correct for distortion of the webcam lenses (which is turning out to be a real drag), I've been keeping myself sane by messing around with a new effect. It uses a perlin noise to change the offset of the texture coordinates, and this is manipulated by music.


Here is the first video, just changing the offset for the texture coordinate of the full RGBA colour vector.

http://www.youtube.com/watch?v=fIyLTT4gg1s

This is doing the same thing but this time changing the offset of the red, green and blue channels separately.

http://www.youtube.com/watch?v=iyl-pukqIjM

This is doing something similar, but slightly more complicated. It is still being split into three, but this time it is sampling in the [CIE XYZ]("http://en.wikipedia.org/wiki/CIE_1931_color_space") colour space (a device independent colour space), and, unlike sRGB, can represent all colours that can possibly exist. I wrote my own conversion algorithm in GLSL, here a sample of the code:
```
vec4 RGBtoXYZ(vec4 colRGB)
{
    float var_R = (colRGB.x/255.0);
    float var_G = (colRGB.y/255.0);
    float var_B = (colRGB.z/255.0);
    
    if( var_R > 0.04045)
    {
        var_R = pow(( (var_R + 0.055) / 1.055 ) , 2.2);
    }
    else
    {
        var_R = (var_R / 12.92);
    }
    if (var_G > 0.04045)
    {
        var_G = pow(( (var_G + 0.055) / 1.055 ) , 2.2);
        
    }
    else
    {
        var_G = (var_G / 12.92);
    }
    if (var_B > 0.04045)
    {
        var_B = pow(( (var_B + 0.055) / 1.055 ) , 2.2);
    }
    else
    {
        var_B = (var_B / 12.92);
    }
    
    var_R = var_R * 100.0;
    var_G = var_G * 100.0;
    var_B = var_B * 100.0;
    
    float var_X = (var_R * 0.4124) + (var_G * 0.3576) + (var_B * 0.1805);
    float var_Y = (var_R * 0.2126) + (var_G * 0.7152) + (var_B * 0.0722);
    float var_Z = (var_R * 0.0193) + (var_G * 0.1192) + (var_B * 0.9505);
    
    return vec4(var_X,var_Y,var_Z,1.0);
}

```
And here is the video

http://www.youtube.com/watch?v=Ut7bkhviGAM&feature=youtu.be

I also attempted doing the same thing in the HSV colour space, and the  colour space (a colour space where linear numerical changes correspond to perceptually equivalent changes in colour). However for this type of colour offsetting effect the outcome was not particularly interesting (very little difference from not splitting the channels at all). I imagine this is because the brightness is represented by one value in both of these colour spaces, as opposed to being a summation of all three channels. 

However It will be really useful going forward to being able to manipulate colours in these colour spaces, especially the LAB colour space as it is so strongly related to human perception.

I also attempted doing the same thing in the HSV colour space and the [CIE LAB]("http://en.wikipedia.org/wiki/Lab_color_space") colour space, which is a colour space where linear numerical changes correspond to perceptually equivalent changes in colour. For this type of colour offsetting effect, however, there was very little difference from not splitting the channels at all, and so the outcome was not particularly interesting. I imagine this is because the brightness is represented by one value in both of these colour spaces, as opposed to being a summation of all three channels.

Despite this it will be really useful going forward to be able to manipulate colours in these colour spaces, especially the LAB colour space as it is so strongly related to human perception.


##Link to prototype

[The code for this is on github](https://github.com/terrybroad/Music_Re-Visualiser)

