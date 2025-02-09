# wf-recorder

wf-recorder is a utility program for screen recording of `wlroots`-based compositors (more specifically, those that support `wlr-screencopy-v1` and `xdg-output`). Its dependences are `ffmpeg`, `wayland-client` and `wayland-protocols`.

# installation

## archlinux

Arch users can use [wf-recorder-git](https://aur.archlinux.org/packages/wf-recorder-git/) from the AUR.
```
yay -S wf-recorder-git 
```


## from source

```
git clone https://github.com/ammen99/wf-recorder.git && cd wf-recorder
meson build --prefix=/usr --buildtype=release
ninja -C build
```
Optionally configure with `-Ddefault_codec='codec'`. The default is libx264. Now you can just run `./build/wf-recorder` or install it with `sudo ninja -C build install`.

Optionally install `scdoc`, a tool by ddevault, for building the manpage.

# usage
In it's simplest form, run `wf-recorder` to start recording and use Ctrl+C to stop. This will create a file called recording.mp4 in the current working directory using the default codec.

Use `-f <filename>` to specify the output file. In case of multiple outputs, you'll first be prompted to select the output you want to record. If you know the output name beforehand, you can use the `-o <output name>` option. 

To select a specific part of the screen you can either use the `-g <geometry>`, or use [slurp](https://github.com/emersion/slurp) for interactive selection of the area
```
wf-recorder -g "$(slurp)"
``` 
to select and limit the recording to a part of the screen.

To specify a codec, use the `-c <codec>` option. To modify codec parameters, use `-p <option_name>=<option_value>`

To use gpu encoding, use a VAAPI codec (for ex. `h264_vaapi`) and specify a GPU device to use with the `-d` option:
```
wf-recorder -f test-vaapi.mkv -c h264_vaapi -d /dev/dri/renderD128
```
Some drivers report support for rgb0 data for vaapi input but really only support yuv planar formats. In this case, use the `-t` or `--force-yuv` option in addition to the vaapi options to convert the data to yuv planar data before sending it to the gpu.

The `-e` option attempts to use OpenCL if wf-recorder was built with OpenCL support and `-t` or `--force-yuv` is specified, even without vaapi gpu encoding. Use `-e#` or `--opencl=#` to use a specific OpenCL device, where `#` is one of the devices listed.
