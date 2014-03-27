So I have uploaded to code for the [Final Prototype](https://github.com/terrybroad/devart-template/tree/master/project_code) onto github. It very closely follows the class structure I have outlined in my class diagram.
Where the functionality is split into trigger and effect classes, (where all of the different triggers and classes inherit from the Trigger and Effect base classes). They pass between them polymorphic datapacket structs and implicity behave in different ways depending on what type of datapacket it is. 
I have not implemented stackable joiner classes yet, where lots of effects could be used one on top of each other but for this simple prototype it was not neccesary to implement. 


This is the Trigger base class. The minimum functionality for the trigger class is to be able to edit and return a datapacket.
```
class Trigger
{
    protected :
    
    DataPacket dataP;

    public :
    
    Trigger(){ }
    
    DataPacket returnData()
    {
        return dataP;
    }

    ~Trigger(){ }
};
```

This is the header file for the SoundAmp trigger class, as you can see the function returnData returns a OscilatePacket as apposed to a normal dataPacket. It also has an extra update function and a toggle sound function for turning the song on and off.
```
class SoundAmp : public Trigger
{
    OscilatePacket dataP;
    
    ofSoundPlayer song;
    int nBandsToGet;
    float * fftSmoothed;
    float avgSound[3];
    float centroid;
    
    public :
    
    SoundAmp(){};
    void update();
    OscilatePacket returnData();
    void toggleSound();
    ~SoundAmp();
    
};
```
This is the datapacket struct. I has an array of 10 floats and three bools. My types may be added to this if there is a more complicated trigger, but this is more than necessary for now. 
```
struct DataPacket
{
    const static int valuesLength = 10;
    const static int truthsLength = 3;
    
    int valuesUsed, truthsUsed;
    
    float values[valuesLength];
    bool truths[truthsLength];
    
    DataPacket()
    {
        for(int i = 0; i < valuesLength; i++)
        {
            values[i] = 0;
            
            if(i < truthsLength)
            {
                truths[i] = false;
            }
        }
    }
};
```

This is the oscillate data packet, the only difference is that it declares how many values in the array it actually uses.
```
struct OscilatePacket : DataPacket
{
    OscilatePacket()
    {
        valuesUsed = 3;
        truthsUsed = 0;
    }
};
```

This is the Effect base class. All effect classes have at least one shader and two frame buffers. The texture from the vidGrabber or framebuffer is passed into the class as well as the dataPacket struct which contains the parameters for controlling the effect; the effect is rendered then a pair of two framebuffers is returned. The textures and framebuffers are passed by reference to be efficient.
```
class Effect
{
    protected :
    vector< ofFbo > framebuf;
    ofShader effectShader;
    
    public :
    
    Effect();
    vector< ofFbo >& renderEffect(vector< ofTexture > &tex, DataPacket dataP);
    ~Effect();
};
```

This is the perlinXYZ effect class, it loads in the shader. And then uses a for loop to render two different framebuffers place them in a vector class and return a reference to them to the main programme. 
```
class PerlinXYZ : public Effect
{
    public :
    
    PerlinXYZ()
    {
        effectShader.load("shaders/perlinXYZ");
    }
    
    vector< ofFbo >& renderEffect(vector< ofTexture > &tex, OscilatePacket dataP)
    {
        for(int i = 0; i < 2; i++)
        {
            framebuf.at(i).begin();
            tex.at(i).bind();
            
            effectShader.begin();
            effectShader.setUniformTexture("tex", tex.at(i), 0);
            effectShader.setUniform1f("changingA", dataP.values[0]*0.2);
            effectShader.setUniform1f("changingL", dataP.values[1]*0.5);
            effectShader.setUniform1f("changingB", dataP.values[2]*5);
            effectShader.setUniform1f("time", ofGetElapsedTimeMillis());
            glBegin(GL_QUADS);
            glTexCoord2f(0,0); glVertex3f(0,0,0);
            glTexCoord2f(1,0); glVertex3f(640,0,0);
            glTexCoord2f(1,1); glVertex3f(640,800,0);
            glTexCoord2f(0,1); glVertex3f(0,800,0);
            glEnd();
            ofPopMatrix();
            
            effectShader.end();
            
            tex.at(i).unbind();
            framebuf.at(i).end();
        }
        
        return framebuf;
    }
    
    ~PerlinXYZ()
    {
        effectShader.unload();
    }
};
```

And this is where the rendering is done in testApp::draw(). It gets the fbo's by calling the renderEffect function and passing in the webcam image textures, and the datapacket from the trigger class.
It then draws the fbo's on left and right hand side for the screen. Applying the shader which compensates for the distortion of the oculus rift and webcam lenses. 
```
    switch(switchInt)
    {
        case 0:
        fbo = effect.renderEffect(tex,trigger.returnData());
        break;
        
        case 1:
            fbo = posterize.renderEffect(tex,trigger.returnData());
        break;
        
        case 2:
            fbo = posterizergb.renderEffect(tex,trigger.returnData());
        break;
        
        case 3:
            fbo = abberation.renderEffect(tex,trigger.returnData());
        break;
        
        case 4:
            fbo = perlinxyz.renderEffect(tex,trigger.returnData());
        break;
        
        case 5:
        fbo = perlinrgb.renderEffect(tex,trigger.returnData());
        break;
        
        default:
            fbo = effect.renderEffect(tex,trigger.returnData());
        break;
    }

    fbo.at(0).getTextureReference().bind();

    hmdWarpShader.begin();
    hmdWarpShader.setUniformTexture("tex", fbo.at(0).getTextureReference(), 0);
    hmdWarpShader.setUniform2f("LensCenter", DistortionXCenterOffset, 0 );
    hmdWarpShader.setUniform2f("ScreenCenter", _x + _w*1.0f, _y + _h*1.0f );
    hmdWarpShader.setUniform2f("Scale", (_w/1.0f) * 1.0f, (_h/1.0f) * 1.0f * as );
    hmdWarpShader.setUniform2f("ScaleIn", (1.0f/_w), (1.0f/_h) / as );
    hmdWarpShader.setUniform4f("HmdWarpParam", K0, K1, K2, K3 );

    glBegin(GL_QUADS);
    glTexCoord2f(1,0); glVertex3f(0,0,0);
    glTexCoord2f(0,0); glVertex3f(640,0,0);
    glTexCoord2f(0,1); glVertex3f(640,800,0);
    glTexCoord2f(1,1); glVertex3f(0,800,0);
    glEnd();
    
    hmdWarpShader.end();
    
    fbo.at(0).getTextureReference().unbind();

    // -----------

    fbo.at(1).getTextureReference().bind();

    hmdWarpShader.begin();
    hmdWarpShader.setUniformTexture("tex", fbo.at(1).getTextureReference(), 0);
    hmdWarpShader.setUniform2f("LensCenter", DistortionXCenterOffset , 1);
    hmdWarpShader.setUniform2f("ScreenCenter", _x + _w*1.0f, _y + _h*1.0f );
    hmdWarpShader.setUniform2f("Scale", (_w/1.0f) * 1.0f, (_h/1.0f) * 1.0f * as );
    hmdWarpShader.setUniform2f("ScaleIn", (1.0f/_w), (1.0f/_h) / as );
    hmdWarpShader.setUniform4f("HmdWarpParam", K0, K1, K2, K3 );
     
    ofPushMatrix();
    ofTranslate(640,0);
    glBegin(GL_QUADS);
    glTexCoord2f(1,0); glVertex3f(0,0,0);
    glTexCoord2f(0,0); glVertex3f(640,0,0);
    glTexCoord2f(0,1); glVertex3f(640,800,0);
    glTexCoord2f(1,1); glVertex3f(0,800,0);
    glEnd();
    ofPopMatrix();
    hmdWarpShader.end();
    fbo.at(1).getTextureReference().unbind();

```

And here is the code for the fragment shader to do that. I ported this into GLSL from the CG shader code [Will Steptoe](http://willsteptoe.com/) posted online.
```
uniform sampler2DRect tex;
uniform vec2 LensCenter; 
uniform vec2 ScreenCenter; 
uniform vec2 Scale; 
uniform vec2 ScaleIn; 
uniform vec4 HmdWarpParam;

varying vec2 texcoord0;

vec2 Warp(vec2 texc)
{
    vec2 center = vec2(0.5,0.5);
    vec2 ff = vec2(640.0, 800.0);
    vec2 normTexCoord = texc / ff;
    vec2 xy = (normTexCoord - center * ScaleIn);
    xy = (texc - (vec2(320.0,400.0) / Scale ))/ff;
    
    float r = sqrt(dot(xy,xy));
    float r2 = r * r;
    float r4 = r2 * r2;
    float coeff = ((HmdWarpParam.x * r2) + (HmdWarpParam.y * r4));
    float dx = HmdWarpParam.z * (2.0 * xy.x * xy.y) + HmdWarpParam.w * (r2 + 2.0 * xy.x * xy.x);
    float dy = HmdWarpParam.z * (r2 + 2.0 * xy.y * xy.y ) + HmdWarpParam.w * ( 2.0 * xy.x * xy.y);
    
    if(LensCenter.y == 0.0)
    {
        xy = ( (xy + (xy * coeff) + vec2(dx,dy) ) * ff * Scale)  + vec2(320.0,400.0) + vec2(LensCenter.x,0.0);
    }
    else
    {
        xy = ( (xy + (xy * coeff) + vec2(dx,dy) ) * ff * Scale)  + vec2(320.0,400.0) - vec2(LensCenter.x,0.0);
    }
    
    return xy;
}

void main()
{
    vec2 coord = Warp(texcoord0);

    if(coord.x < 0.0 || coord.x > 640.0 || coord.y < 0.0 || coord.y > 640.0 )
    {
        gl_FragColor = vec4(0.0,0.0,0.0,1.0);
    }
    else
    {
        gl_FragColor = texture2DRect(tex, coord);
    }
}
```
