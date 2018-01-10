# Emacs supporting pure-gtk3

In this fork, I'm working to make Emacs support pure-gtk3, in order to support Wayland.

Maybe It's only for Wayland now.

The work is in progress.

## Building

You need cairo.

```sh
git clone https://github.com/masm11/emacs.git
cd emacs
./autogen.sh
./configure --without-x --with-cairo --with-modules
make
```

Ignore many warnings.

#### For archlinux users

This repository contains PKGBUILD, so you can do to install:

```
mkdir /tmp/emacs
cd /tmp/emacs
wget https://raw.githubusercontent.com/masm11/emacs/master/PKGBUILD
makepkg -s
sudo pacman -U emacs-pgtk*.tar.xz
```

## Running

```sh
cd src
GDK_BACKEND=wayland ./emacs  (or ./emacs -Q if you have problems.)
```

## X11 and Wayland

Of course, PGTK supports X11 and Wayland connections.

You can use `GDK_BACKEND` environment variable and `--display` option,
and you can do `(make-frame-on-display display-name)` with display-name of
different backend from the first frame.

You can know which backend is used for a frame:

```elisp
(pgtk-backend-display-class)
```

This returns `"GdkWaylandDisplay"` for Wayland, or `"GdkX11Display"` for X11.

Note: Segmentation fault may occur on multiple display environment.

## TODO

- segmentation fault while multiple-display.
- sometimes can't send selection data

Low priority:
- toolbar.
- menubar.
- so many features not supported.

Those may not be developed because I don't use them.

## ImageMagick

Emacs doesn't support ImageMagick 7.

I wrote this code in configure.ac:

```sh
    if test $HAVE_IMAGEMAGICK != yes; then
      IMAGEMAGICK_MODULE="MagickWand-6.Q16HDRI >= 6.3.5 MagickWand-6.Q16HDRI != 6.8.2 MagickWand-6.Q16HDRI < 7 MagickCore-6.Q16HDRI >= 6.9.9 MagickCore-6.Q16HDRI < 7"
      EMACS_CHECK_MODULES([IMAGEMAGICK], [$IMAGEMAGICK_MODULE])
    fi
```

However, MagickWand-6.Q16HDRI requires MagickCore, which may be ImageMagick 7.
So, you may need to fix `Requires:` in `/usr/lib/pkgconfig/MagickWand-6.Q16HDRI.pc`:

```
Requires: MagickCore-6.Q16HDRI
```

## Debugging

Edit src/pgtkterm.h to uncomment:

```c
#define PGTK_DEBUG 1
```

It enables many many debugging outputs.

On gdb, you may want to do:

```
(gdb) handle SIGPIPE nostop noprint
```

## My Environment

- archlinux
- gtk+-3.22.26
- glib-2.54.2
- gnome-shell 3.26.2
- gcc 7.2.1
- wayland 1.14.0
- wayland-protocols 1.12

## Notice

- This code is NOT completely pure.

  It uses backend-specific functions to obtain the socket file
  descriptor to the display server. It supports X11 and Wayland.

- Commit messages are in Japanese.

## About me

masm11.
