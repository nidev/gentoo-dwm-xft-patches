# Gentoo DWM Xft

This repo provides proper patches for dwm during epatch_user (patch from /etc/portage/patches/category/application) stage.

## Background knowledge

[dwm](http://dwm.suckless.org/) is a window manager for X windows system, which tiles windows automatically. It is simple and light. Modifying config.h and applying various patches can change behavior/appearance of dwm.

Applying Xft patch will make the appearance of title bar font better. By declaring your preferred font in Xft way (like 'Droid Sans Fallback:size=12'), you can change the font.

Portage is a package management system for Gentoo Linux. It allows a user (in common meaning) to add customized patches without modifying ebuild(s). This stage is done by *epatch_user* function call. *epatch_user* will search patches from proper location like, for example, /etc/portage/patches/category/application. For dwm, it will be /etc/portage/patches/x11-wm/dwm.

## Why not just putting dwm-6.0-xft.diff into user patch location?

You can get Xft patch from [here](http://dwm.suckless.org/patches/xft). But you can't directly apply it with dwm ebuild. Look at the ebuild:
```shell
src_prepare() {
	sed -i \
		-e "s/CFLAGS = -std=c99 -pedantic -Wall -Os/CFLAGS += -std=c99 -pedantic -Wall/" \
		-e "/^LDFLAGS/{s|=|+=|g;s|-s ||g}" \
		-e "s/#XINERAMALIBS =/XINERAMALIBS ?=/" \
		-e "s/#XINERAMAFLAGS =/XINERAMAFLAGS ?=/" \
		-e "s@/usr/X11R6/include@${EPREFIX}/usr/include/X11@" \
		-e "s@/usr/X11R6/lib@${EPREFIX}/usr/lib@" \
		-e "s@-I/usr/include@@" -e "s@-L/usr/lib@@" \
		config.mk || die
	sed -i \
		-e '/@echo CC/d' \
		-e 's|@${CC}|$(CC)|g' \
		Makefile || die

	restore_config config.h
	epatch_user
}
```

*epatch_user* is called after some changes are applied. That's the reason we can not use dwm-6.0-xft.diff directly. The original patch is based on vanilla codes of dwm. So, at the end, some modifications are required.

# Apply patches

See [Gentoo Wiki](https://wiki.gentoo.org/wiki/Dwm#patches_in_.2Fetc.2Fportage.2Fpatches.2Fcategory.2Fapplication).
