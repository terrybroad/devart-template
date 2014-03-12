So i've finally taken the plunge into doing something I've been meaning to do for a long time but have been quite apprehensive about, using audio
analysis to control image manipulation. I had been putting off doing this for a while, as audio analysis is something I've had relatively little
experience in, however it turned out to be a lot easier and more fruitful than I could have imagined!

So here you go, I'm loading in an mp3 file using the ofSoundPlayer class, then i'm just taking a short fft of what is currently being played and
summing the medium, low and high frequencies, to get floats which represent the relative amplitudes. Then using this to control parameters in the
fragment shaders.

Here are the videos of my progress, I would highly recommend watching them in HD.

This is a simple posterize effect, where the threshold is being controlled by the low frequencies.

http://www.youtube.com/watch?v=pjrtkVok8SE

This is a very similar effect, but in this case threshold for the red green and blue channels are being controlled
separately by the low, medium and high frequencies.

http://www.youtube.com/watch?v=N-EsaaBtW4s

This is offsetting the texture reference by a sine function, where the frequency of the sine function is being controlled by the low frequencies.

http://www.youtube.com/watch?v=_vFZXHKl7CA

This is offsetting the texture reference for each colour channel linearly, where the red green and blue channels are being controlled seperately by the low, medium and high frequencies.

http://www.youtube.com/watch?v=u_LzU2jVCmo

This is combining the aberration and wobble effects, to offset the texture reference for each colour channel, but this time using the sine function where the frequency increases and the red green and blue channels are being controlled seperately by the low, medium and high frequencies.

http://www.youtube.com/watch?v=q7SoNylkKKM

##Link to prototype 

[The code for this is on github](https://github.com/terrybroad/Music_Re-Visualiser)

##TO DO:

Develop more sophisticated audio feature extraction, maybe use MFCC to detect pitch and key, and use the spectral centroid to detect timbre.

Re-Build the oculus rift rig, im pretty fed up of filming myself by now and really want to see what these effects look like on the rift!
