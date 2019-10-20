Sublime-AviUtl-Script
=====================

Configuration files of AviUtl for Sublime Text 3

ToDo
----

-[ ] include `rikky_module` functions;
-[ ] show function usages;
-[ ] migrate to VSCode, Atom;

References
----------

Most of the keywords are refered in https://ch.nicovideo.jp/usunoro/blomaga/ar915424.

```yaml
- match: '\b(antialias|billboard|blend|culling|shadow)|(audiobuffer|drawtarget|(figure|framebuffer|image|movie|tempbuffer|text))|alpha_add|alpha_sub|camera_mode|camera_param|col|focus_mode|fourier|gui|multi_object|pcm|rgb|saving|script_name|script_path|section_num|spectrum\b'
```

```yaml
- match: '\b(load)\b'
  scope: support.function.library.aviutl-script
  push:
    - match: '"(figure|framebuffer|image|movie|tempbuffer|text)"'
      pop: true
- match: '\b(getinfo)\b'
  scope: support.function.library.aviutl-script
  push:
    - match: '"(script_path|saving)"'
      pop: true
- match: '\b(copybuffer)\b'
  scope: support.function.library.aviutl-script
  push:
    - match: '"(tmp|obj|cache:\w{4})",\s*"(frm|obj|tmp|cache:\w{4}|image:\w{4})"'
      pop: true
- match: '\b(getaudio)\b'
  scope: support.function.library.aviutl-script
  push:
    - match: '"(fourier|spectrum|pcm)"'
      pop: true
- match: '\b(getoption)\b'
  scope: support.function.library.aviutl-script
  push:
    - match: '"(script_name|gui|camera_mode|camera_param|multi_object)"'
      pop: true
- match: '\b(setoption)\b'
  scope: support.function.library.aviutl-script
  push:
  - match: '"(culling|billboard|shadow|antialias|blend|drawtarget|draw_state|focus_mode|camera_param)"'
    pop: true
```