# mpv
1. 安装常用依赖项：
   ```bash
   sudo apt install -y build-essential git nasm yasm autoconf automake autotools-dev libtool libfribidi-dev pkg-config perl libbluray-dev libdvdread-dev libdvdnav-dev libcdio-cdda-dev libuchardet-dev librubberband-dev liblcms2-dev libarchive-dev libsdl2-dev libpulse-dev libjack-dev libasound2-dev wayland-protocols libx11-dev x11proto-scrnsaver-dev libxext-dev libxinerama-dev libfontconfig1-dev libfreetype6-dev libxfixes-dev libxi-dev libxrender-dev libxcb1-dev libx11-xcb-dev libxcb-glx0-dev libxrandr-dev libgdm-dev libwayland-dev libvdpau-dev libva-dev libegl1-mesa-dev libluajit-5.1-dev libgles2-mesa-dev libgl1-mesa-dev libjpeg-dev youtube-dl libssl-dev libpng-dev libharfbuzz-dev openssl
   ```
   部分需要自己下载源码构建的包：
   - libshaderc：https://github.com/google/shaderc
      - 依赖：https://github.com/google/googletest
   - VapourSynth：https://github.com/vapoursynth/vapoursynth
      - 依赖：https://github.com/buaazp/zimg
   - ffnvcodec：https://github.com/FFmpeg/nv-codec-headers
   - MuJS：https://github.com/ccxvii/mujs
2. 【可选】安装编译器Clang：
   ```bash
   sudo apt install -y clang clang-tools lld libc++-dev libc++abi-dev libomp-dev libfuzzer-N-dev
   ```
3. 下载构建脚本：https://github.com/mpv-player/mpv-build
4. 将构建选项输出到脚本目录下的`ffmpeg_options`和`mpv_options`文件，例如：
   ```bash
   echo --enable-small > ffmpeg_options
   echo --disable-avresample >> ffmpeg_options
   echo --disable-muxers >> ffmpeg_options
   echo --disable-encoders >> ffmpeg_options
   echo --disable-filters >> ffmpeg_options
   echo --enable-filter=*fade,*fifo,*format,*resample,aeval,all*,atempo,color*,convolution,draw*,eq*,framerate,hw*,null,volume >> ffmpeg_options
   echo --disable-decoders >> ffmpeg_options
   echo --enable-decoder=*sub*,*text*,*web*,aac*,ac3*,alac*,ape,ass,cc_dec,cook,dca,eac3*,truehd,flv,flac,gif,h264*,hevc*,mp[1-3]*,*peg*,mlp,mpl2,nellymoser,opus,pcm*,rawvideo,rv*,sami,srt,ssa,v210*,vc1*,vorbis,vp[6-9],wm*,wrapped_avframe >> ffmpeg_options
   echo --disable-demuxers >> ffmpeg_options
   echo --enable-demuxer=*sub*,*text*,*ac3,*ac,*peg*,*web*,ape,ass,avi,concat,dts*,*dash*,*flv,gif,hls,h264,hevc,matroska,mlv,mov,mp3,mxf,nsv,nut,ogg,pcm*,rawvideo,rt*p,spdif,srt,vc1,v210*,wav,*pipe >> ffmpeg_options
   echo --disable-parsers >> ffmpeg_options
   echo --enable-parser=*sub*,aac*,ac3,cook,flac,h26[3-4],hevc,m*,opus,rv*,vc1,vorbis,vp[8-9] >> ffmpeg_options
   echo --enable-static-build > mpv_options
   echo --enable-libmpv-shared >> mpv_options
   # 只有一个“>”时，输出的内容会覆盖原来的文件
   # 当使用“>>”时，输出的内容会追加到原来的文件的末尾
   ```
5. `./rebuild -j6`
6. 生成的文件位于`./mpv/build`下
