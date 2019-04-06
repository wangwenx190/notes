# mpv
1. 安装常用依赖项：
   ```bash
   sudo apt install -y build-essential git nasm yasm autoconf automake autotools-dev libtool libfribidi-dev pkg-config perl libbluray-dev libdvdread-dev libdvdnav-dev libcdio-cdda-dev libuchardet-dev librubberband-dev liblcms2-dev libarchive-dev libsdl2-dev libsdl2-gfx-dev libsdl2-image-dev libsdl2-mixer-dev libsdl2-net-dev libsdl2-ttf-dev libpulse-dev libjack-dev libasound2-dev wayland-protocols libx11-dev x11proto-scrnsaver-dev libxext-dev libxinerama-dev libfontconfig1-dev libfreetype6-dev libxfixes-dev libxi-dev libxrender-dev libxcb1-dev libx11-xcb-dev libxcb-glx0-dev libxrandr-dev libgdm-dev libwayland-dev libvdpau-dev libva-dev freeglut3-dev libegl1-mesa-dev libgegl-dev libluajit-5.1-dev libgles2-mesa-dev libgl1-mesa-dev libjpeg-dev youtube-dl libssl-dev libcrypto++-dev libpng-dev
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
   echo --enable-libx264 > ffmpeg_options
   echo --enable-libmp3lame >> ffmpeg_options
   echo --enable-libmpv-shared > mpv_options
   # 只有一个“>”时，输出的内容会覆盖原来的文件
   # 当使用“>>”时，输出的内容会追加到原来的文件的末尾
   ```
5. `./rebuild -j6`
6. 生成的文件位于`./mpv/build`下
