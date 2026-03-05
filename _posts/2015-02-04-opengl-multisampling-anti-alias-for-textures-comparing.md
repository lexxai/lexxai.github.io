---
layout: post
title: "OpenGL multisampling anti-alias for textures, comparing"
date: 2015-02-04 23:06:00 +0000
tags: []
blogger_orig_link: https://lexxai.blogspot.com/2015/02/opengl-multisampling-anti-alias-for.html
---

[![](/assets/images/blog/1ecfca201cef06e9-73d564086c2e9ac1.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEhTEPMSjbQjI_6pNkM7LNjER2zKALEkzJDaj7sxqV4wfftwpFqkw4prY28Yn-_EgbkUHq9GMV5xRGPc7A9WyHWlv96LOq8HOLymhJwOeqDbI4CYzeMrwb19x7xXkZ3hIJuxgGa1kb1tBg24/s1600/2015-02-03+21-26-17.png)  
*Samples 0, Texture mode: Nearest*

  
Used, Qt 5.4 OpenGL x64, Visual Studio 2013 SP4, (Community Edition)  
  
Examples of "opengl\cube" - Examples\Qt-5.4\opengl\cube  
  
main.cpp:  

```
int main(int argc, char *argv[]) {
 QApplication app(argc, argv);
 QSurfaceFormat format;
 format.setDepthBufferSize(24);
 # Set the preferred number of samples per pixel when multisampling 
 # is enabled to numSamples. By default, multisampling is disabled. 
 format.setSamples(16)
 QSurfaceFormat::setDefaultFormat(format);
```

mainwidget.cpp:  

```
void MainWidget::initializeGL(){
...  
 initializeOpenGLFunctions();
 qDebug("Samples %d",format.samples()); 
 #max size of texture OpenGL
 GLint texSize;
 glGetIntegerv(GL_MAX_TEXTURE_SIZE, &texSize);
 qDebug() << texSize; 
 
 #list of extensions OpenGL 
 QSurfaceFormat format = QOpenGLContext::currentContext()->format();
 QSet <QByteArray>extensions= QOpenGLContext::currentContext()->extensions();
 qDebug() << extensions.count();
 foreach (const QByteArray &value, extensions)
 qDebug() << value; 
...
```

[![](/assets/images/blog/5602be90d199b11c-ef10f8d73e211d86.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgh2Uka7IJ7u-463uA7vfUjAMzOj-ClnUNxOqdPnGVavgBiTkhZYxO5RPxHiugo2IeBToBxqdgC7McdPb2Lx1JCO6A6bNK4NS7ITurb951u8OXIwmRQBqkGG1gB39z_br4_ubqcmjQmjrbC/s1600/2015-02-03+21-30-19.png)  
*Samples 0, Texture mode: Linear*

[![](/assets/images/blog/22d40fdbf20544ff-308254d6003c070c.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEiKEdZ29wKHmiNEQ1Cz4scdoTFtOBEg0jak9fHkEzF9lD4g37xX_4G9LUrO71SAMPps6Z4D7zYiuguz9jtvfjsnTeQKK0LvF1LUjGOIW7inno4CgS_VonrJ5ObQLVz5-dUQBIfG4wPdrwus/s1600/2015-02-03+21-32-29.png)  
*Samples 0, Texture mode: LinearMipLinear*

[![](/assets/images/blog/7815ee36315233be-f34d5c57429df802.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEi5Ql_K166KnF-E271cetwXxVs-0WnIl9uAO6qYl5MKAswEFm1WBxLOFVJ25hF9JJWQB2rPQMMNSMTTi83yLApd1Cj-Sch7hWgoucdOz1d7JrMM67bTStnL1Ce2rn7YJfZ5GwHVba2ZO2G-/s1600/2015-02-04+2-12-04.png)  
*Samples 16, Texture mode: Nearest*

[![](/assets/images/blog/44a90d92eeedc6c1-89ed975498c3c35f.png)](https://blogger.googleusercontent.com/img/b/R29vZ2xl/AVvXsEgUL-Gl2FGXcEdS3HLAt1kcnFr08h3-zPH085ujt_KQe_2rusv3kdocspWDYpm1jRanGC4aTb6_3OBpazj2BImvhOF3FGR0OLtzqAp-S3oOXbN84bbr1IubxrFxC5nnYuPw-B30BUCbI93Q/s1600/2015-02-04+2-14-27.png)  
*Samples 16, Texture mode: Linear*

  
  
  
cube.exe...  

Samples 16

8192   

238

"GL\_ARB\_clear\_buffer\_object"

"GL\_ARB\_vertex\_program"

"GL\_ARB\_texture\_storage\_multisample"

"GL\_ARB\_framebuffer\_sRGB"

"GL\_NV\_vertex\_program3"

"GL\_ARB\_vertex\_shader"

"GL\_EXT\_stencil\_wrap"

"GL\_ARB\_viewport\_array"

"GL\_ARB\_pixel\_buffer\_object"

"GL\_ARB\_compatibility"

"GL\_EXT\_vertex\_array\_bgra"

"GL\_EXT\_framebuffer\_multisample"

"GL\_EXT\_depth\_bounds\_test"

"GL\_ARB\_texture\_buffer\_object"

"GL\_EXT\_texture\_lod"

"GL\_NV\_fragment\_program\_option"

"GL\_ARB\_shader\_texture\_lod"

"GL\_ARB\_shading\_language\_420pack"

"GL\_WIN\_swap\_hint"

"GL\_ARB\_multisample"

"GL\_ARB\_texture\_cube\_map"

"GL\_ARB\_draw\_instanced"

"GL\_NV\_multisample\_filter\_hint"

"GL\_ARB\_compressed\_texture\_pixel\_storage"

"GL\_ARB\_depth\_buffer\_float"

"GL\_EXT\_texture\_shared\_exponent"

"GL\_NV\_conditional\_render"

"GL\_NV\_texture\_env\_combine4"

"GL\_ARB\_texture\_compression\_rgtc"

"GL\_NV\_vertex\_program2\_option"

"GL\_SGIX\_depth\_texture"

"GL\_NV\_parameter\_buffer\_object"

"GL\_ARB\_multitexture"

"GL\_EXT\_bindable\_uniform"

"GL\_ARB\_vertex\_buffer\_object"

"GL\_EXT\_texture\_compression\_rgtc"

"GL\_ARB\_copy\_image"

"GL\_EXT\_direct\_state\_access"

"GL\_NV\_register\_combiners2"

"GL\_ARB\_texture\_mirror\_clamp\_to\_edge"

"GL\_ARB\_shading\_language\_packing"

"GL\_NV\_blend\_square"

"GL\_ARB\_explicit\_uniform\_location"

"GL\_EXT\_texture\_buffer\_object"

"GL\_NV\_parameter\_buffer\_object2"

"GL\_ARB\_texture\_rgb10\_a2ui"

"GL\_ARB\_draw\_elements\_base\_vertex"

"GL\_EXT\_texture\_sRGB\_decode"

"GL\_ARB\_debug\_output"

"GL\_NV\_texture\_rectangle"

"GL\_KHR\_debug"

"GL\_KTX\_buffer\_region"

"GL\_ARB\_shading\_language\_include"

"GL\_EXT\_framebuffer\_sRGB"

"GL\_EXTX\_framebuffer\_mixed\_formats"

"GL\_ARB\_texture\_buffer\_range"

"GL\_EXT\_texture\_sRGB"

"GL\_NV\_shader\_buffer\_load"

"GL\_EXT\_abgr"

"GL\_EXT\_packed\_depth\_stencil"

"GL\_ARB\_uniform\_buffer\_object"

"GL\_ARB\_fragment\_layer\_viewport"

"GL\_EXT\_texture\_compression\_s3tc"

"GL\_ARB\_fragment\_program"

"GL\_NV\_ES1\_1\_compatibility"

"GL\_EXT\_multi\_draw\_arrays"

"GL\_ARB\_separate\_shader\_objects"

"GL\_ARB\_framebuffer\_object"

"GL\_NV\_transform\_feedback"

"GL\_NV\_vertex\_program"

"GL\_ARB\_half\_float\_pixel"

"GL\_ARB\_half\_float\_vertex"

"GL\_ARB\_texture\_rectangle"

"GL\_EXT\_Cg\_shader"

"GL\_EXT\_packed\_float"

"GL\_NV\_vertex\_program1\_1"

"GL\_ARB\_sync"

"GL\_SUN\_slice\_accum"

"GL\_NV\_vertex\_array\_range2"

"GL\_ARB\_map\_buffer\_alignment"

"GL\_EXT\_texture\_lod\_bias"

"WGL\_EXT\_swap\_control"

"GL\_NV\_texture\_barrier"

"GL\_ARB\_transpose\_matrix"

"GL\_ARB\_enhanced\_layouts"

"GL\_SGIS\_texture\_lod"

"GL\_EXT\_packed\_pixels"

"GL\_ARB\_texture\_storage"

"GL\_IBM\_rasterpos\_clip"

"GL\_NV\_depth\_buffer\_float"

"GL\_ATI\_draw\_buffers"

"GL\_ARB\_texture\_stencil8"

"GL\_ARB\_program\_interface\_query"

"GL\_ARB\_blend\_func\_extended"

"GL\_ARB\_internalformat\_query2"

"GL\_NV\_vertex\_buffer\_unified\_memory"

"GL\_ARB\_depth\_clamp"

"GL\_NV\_occlusion\_query"

"GL\_ARB\_explicit\_attrib\_location"

"GL\_ARB\_seamless\_cube\_map"

"GL\_ARB\_internalformat\_query"

"GL\_ARB\_imaging"

"GL\_NV\_half\_float"

"GL\_ARB\_texture\_swizzle"

"GL\_ARB\_texture\_view"

"GL\_ARB\_occlusion\_query2"

"GL\_ARB\_ES3\_compatibility"

"GL\_NVX\_gpu\_memory\_info"

"GL\_ARB\_multi\_bind"

"GL\_ARB\_invalidate\_subdata"

"GL\_ARB\_sampler\_objects"

"GL\_EXT\_rescale\_normal"

"GL\_ARB\_vertex\_array\_bgra"

"GL\_NV\_texgen\_reflection"

"GL\_NV\_texture\_shader3"

"GL\_ARB\_color\_buffer\_float"

"GL\_ARB\_conservative\_depth"

"GL\_EXT\_stencil\_two\_side"

"GL\_ATI\_texture\_float"

"GL\_EXT\_texture\_mirror\_clamp"

"GL\_ARB\_point\_parameters"

"GL\_ARB\_shader\_bit\_encoding"

"GL\_NV\_fence"

"GL\_ARB\_point\_sprite"

"GL\_ARB\_fragment\_coord\_conventions"

"GL\_S3\_s3tc"

"GL\_EXT\_texture\_edge\_clamp"

"GL\_EXT\_texture\_object"

"GL\_ATI\_texture\_mirror\_once"

"GL\_NV\_depth\_clamp"

"GL\_ARB\_instanced\_arrays"

"GL\_ARB\_texture\_border\_clamp"

"GL\_NV\_fragment\_program2"

"GL\_ARB\_window\_pos"

"GL\_ARB\_texture\_rg"

"GL\_EXT\_gpu\_shader4"

"GL\_NV\_texture\_expand\_normal"

"GL\_NV\_float\_buffer"

"GL\_ARB\_map\_buffer\_range"

"GL\_IBM\_texture\_mirrored\_repeat"

"GL\_EXT\_shader\_integer\_mix"

"GL\_ARB\_copy\_buffer"

"GL\_EXT\_texture\_env\_combine"

"GL\_NV\_texture\_shader"

"GL\_NV\_primitive\_restart"

"GL\_EXT\_texture\_compression\_latc"

"GL\_ARB\_vertex\_array\_object"

"GL\_EXT\_texture\_storage"

"GL\_EXT\_fog\_coord"

"GL\_SGIX\_shadow"

"GL\_EXT\_framebuffer\_blit"

"GL\_EXT\_gpu\_program\_parameters"

"GL\_EXT\_blend\_func\_separate"

"GL\_ARB\_ES2\_compatibility"

"GL\_NV\_fog\_distance"

"GL\_NV\_texture\_compression\_vtc"

"GL\_ARB\_fragment\_shader"

"GL\_EXT\_point\_parameters"

"GL\_EXT\_texture3D"

"GL\_EXT\_texture\_filter\_anisotropic"

"GL\_ARB\_vertex\_type\_2\_10\_10\_10\_rev"

"GL\_ARB\_framebuffer\_no\_attachments"

"GL\_ARB\_vertex\_attrib\_binding"

"GL\_NV\_explicit\_multisample"

"GL\_ARB\_robust\_buffer\_access\_behavior"

"GL\_EXT\_provoking\_vertex"

"GL\_EXT\_texture\_swizzle"

"GL\_ARB\_get\_program\_binary"

"GL\_ARB\_texture\_query\_levels"

"GL\_EXT\_draw\_instanced"

"GL\_EXT\_draw\_buffers2"

"GL\_ARB\_fragment\_program\_shadow"

"GL\_NV\_copy\_depth\_to\_color"

"GL\_EXT\_separate\_shader\_objects"

"GL\_EXT\_texture\_compression\_dxt1"

"GL\_NV\_geometry\_shader4"

"GL\_ARB\_arrays\_of\_arrays"

"GL\_ARB\_robustness"

"GL\_NV\_copy\_image"

"GL\_NV\_gpu\_program4"

"GL\_ARB\_occlusion\_query"

"GL\_ARB\_vertex\_type\_10f\_11f\_11f\_rev"

"GL\_ARB\_texture\_env\_combine"

"GL\_ARB\_texture\_compression"

"GL\_EXT\_import\_sync\_object"

"GL\_NV\_packed\_depth\_stencil"

"GL\_EXT\_framebuffer\_object"

"GL\_EXT\_texture\_cube\_map"

"GL\_ARB\_stencil\_texturing"

"GL\_ARB\_provoking\_vertex"

"GL\_ARB\_depth\_texture"

"GL\_ARB\_geometry\_shader4"

"GL\_EXT\_pixel\_buffer\_object"

"GL\_EXT\_blend\_minmax"

"GL\_NV\_texture\_multisample"

"GL\_ARB\_shader\_objects"

"GL\_EXT\_texture\_env\_dot3"

"GL\_EXT\_bgra"

"GL\_ARB\_texture\_float"

"GL\_EXT\_separate\_specular\_color"

"GL\_ARB\_texture\_non\_power\_of\_two"

"GL\_EXT\_vertex\_array"

"GL\_ARB\_shading\_language\_100"

"GL\_NV\_point\_sprite"

"GL\_EXT\_timer\_query"

"GL\_EXT\_blend\_equation\_separate"

"GL\_EXT\_texture\_env\_add"

"GL\_NV\_vertex\_program2"

"GL\_NV\_vertex\_array\_range"

"GL\_NV\_register\_combiners"

"GL\_NV\_framebuffer\_multisample\_coverage"

"GL\_ARB\_draw\_buffers"

"GL\_NV\_light\_max\_exponent"

"GL\_EXT\_draw\_range\_elements"

"GL\_EXT\_blend\_subtract"

"GL\_ARB\_texture\_env\_dot3"

"GL\_ARB\_base\_instance"

"GL\_EXT\_texture\_array"

"GL\_ARB\_texture\_mirrored\_repeat"

"GL\_NV\_multisample\_coverage"

"GL\_NV\_pixel\_data\_range"

"GL\_ARB\_texture\_env\_add"

"GL\_EXT\_compiled\_vertex\_array"

"GL\_EXT\_shadow\_funcs"

"GL\_NVX\_conditional\_render"

"GL\_EXT\_secondary\_color"

"GL\_NV\_texture\_shader2"

"GL\_EXT\_geometry\_shader4"

"GL\_ARB\_timer\_query"

"GL\_ARB\_texture\_multisample"

"GL\_NV\_path\_rendering"

"GL\_ARB\_shadow"

"GL\_ARB\_texture\_env\_crossbar"

"GL\_SGIS\_generate\_mipmap"

"GL\_NV\_fragment\_program"

"GL\_EXT\_framebuffer\_multisample\_blit\_scaled"

"GL\_EXT\_blend\_color"

"GL\_EXT\_texture\_integer"
