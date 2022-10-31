# meson ray tracer

A simple ray tracer written in the meson.build language.

![render example](final.png)

# usage

```
$meson setup -Ddim=512 build
```

The output will be located at `build/output.ppm`.

# performance

- The example render on this page took 15m13s to render on 1 core of a Intel(R)
  Core(TM) i7-2640M CPU @ 2.80GHz with a `-Doptimization=3` build of muon.
- Meson performance: still running
