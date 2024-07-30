+++
title = "Google Chrome & VS Code Crash on Fedora 39"
date = 2024-07-30
+++

Recently got repetitive crash from `google-chrome` and or visual studio `code`
on my machine. If i look back, it seems the issue occured after upgrading to
Fedora 39. The two application seems lagging after some time and eventually will
crash.

When it chrash, the stdout of it show messages some thing like these:

```
[0730/142933.386222:ERROR:elf_dynamic_array_reader.h(64)] tag not found
[0730/142933.387469:ERROR:elf_dynamic_array_reader.h(64)] tag not found
[0730/142933.388541:ERROR:elf_dynamic_array_reader.h(64)] tag not found
[0730/142933.388842:ERROR:elf_dynamic_array_reader.h(64)] tag not found
[1]    355103 trace trap (core dumped)
```

Today i dig deeper, and found the solution which i think really solve the issue:
Start with option `--ozone-platform=wayland`. The option work for both
`google-chrome` and or `code`, and with this option, the lag seems not occured
again.

```bash
# start google chrome with this option
google-chrome-stable --disable-gpu --ozone-platform=wayland

#
code --ozone-platform=wayland

# below is my env vars related to wayland things
WAYLAND_DISPLAY=wayland-0
XAUTHORITY=/run/user/1000/.mutter-Xwaylandauth.LQ9VR2
XDG_SESSION_TYPE=wayland
```
