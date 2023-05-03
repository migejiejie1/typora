```shell
libjpeg.so.62 -> libjpeg.so.62.0.0
libpng12.so.0 -> libpng12.so.0.44.0
libfontconfig.so.1 -> libfontconfig.so.1.4.4
libXext.so.6 -> libXext.so.6.4.0
libXt.so.6 -> libXt.so.6.0.0
libpangocairo-1.0.so.0 -> libpangocairo-1.0.so.0.2800.1
libpango-1.0.so.0 -> libpango-1.0.so.0.2800.1
libcairo.so.2 -> libcairo.so.2.11000.2
libSM.so.6 -> libSM.so.6.0.0
libICE.so.6 -> libICE.so.6.3.0
libX11.so.6 -> libX11.so.6.3.0
libpangoft2-1.0.so.0 -> libpangoft2-1.0.so.0.2800.1
libpixman-1.so.0 -> libpixman-1.so.0.18.4
libXrender.so.1 -> libXrender.so.1.3.0
libxcb.so.1 -> libxcb.so.1.1.0
libXau.so.6 -> libXau.so.6.0.0

chmod a+x libcairo.so.2.11000.2 libpangocairo-1.0.so.0.2800.1 libXt.so.6.0.0 libXext.so.6.4.0 libX11.so.6.3.0 libSM.so.6.0.0 libpng12.so.0.44.0 libICE.so.6.3.0 libfontconfig.so.1.4.4 libjpeg.so.62.0.0 libpango-1.0.so.0.2800.1 libpangoft2-1.0.so.0.2800.1 libpixman-1.so.0.18.4 libXrender.so.1.3.0 libxcb.so.1.1.0 libXau.so.6.0.0

cd /usr/local/jdk1.7.0_80/bin
ldd libD3Lib.so 

ln -s libjpeg.so.62.0.0 libjpeg.so.62
ln -s libpng12.so.0.44.0 libpng12.so.0
ln -s libfontconfig.so.1.4.4 libfontconfig.so.1
ln -s libXext.so.6.4.0 libXext.so.6

ln -s libXt.so.6.0.0 libXt.so.6
ln -s libpangocairo-1.0.so.0.2800.1 libpangocairo-1.0.so.0
ln -s libpango-1.0.so.0.2800.1 libpango-1.0.so.0
ln -s libcairo.so.2.11000.2 libcairo.so.2

ln -s libSM.so.6.0.0 libSM.so.6 
ln -s libICE.so.6.3.0 libICE.so.6
ln -s libX11.so.6.3.0 libX11.so.6
ln -s libpangoft2-1.0.so.0.2800.1 libpangoft2-1.0.so.0

ln -s libpixman-1.so.0.18.4 libpixman-1.so.0
ln -s libXrender.so.1.3.0 libXrender.so.1
ln -s libxcb.so.1.1.0 libxcb.so.1
ln -s libXau.so.6.0.0 libXau.so.6

```

