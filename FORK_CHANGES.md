# Changes in this fork

This is a fork of [3b1b/manim](https://github.com/3b1b/manim) (ManimGL 1.7.2). It is based on
upstream commit [`fafa083`](https://github.com/3b1b/manim/commit/fafa083a4fb274bba9cabde0b6e2f50ba6da0622)
("Add VideoMobject and Sprite (#2521)", 2026-09-09), and the `fixes` branch adds **bug fixes
only**: no new features and no API changes. Scenes written for upstream ManimGL run the same
way here.

The fixes came up while running the public [3b1b/videos](https://github.com/3b1b/videos) code
on this version. Each one is a single commit whose message explains the cause.

To see every change against upstream:

```bash
git log --reverse fafa083..fixes        # one commit per fix
git diff fafa083 fixes -- manimlib      # the whole code diff
```

## Summary

| # | Commit | Area | Symptom on upstream |
|---|---|---|---|
| 1 | `4d1ee1e8` | `Tex`, `TexText` | Every LaTeX mobject fails with `ParseError: syntax error: line 1, column 0` |
| 2 | `888b597a` | `TexturedSurface` | `NameError: im_coords` on `ShowCreation` and other partial animations |
| 3 | `1aae293e` | `AnimatedStreamLines` | `TypeError: unexpected keyword argument 'frame_rate'` on every frame |
| 4 | `7abd327c` | `AnimationGroup`, `LaggedStart` | `Only VMobjects can be passed into VGroup` when given a generator of non-VMobject animations |
| 5 | `0cf2b238` | `DotCloud`, `GlowDots` | `ValueError` (max of an empty array) when an empty cloud is animated |
| 6 | `d433356a` | `TexturedGeometry`, `ThreeDModel` | Shader compile error (`DarkTexture`) and `AttributeError` when drawn |

## Details

### 1. LaTeX output corrupted by a Ghostscript notice

- **File:** `manimlib/utils/tex_file_writing.py` (`full_tex_to_svg`)
- **Cause:** dvisvgm 3.0.x passes `-dNEWPDF` to Ghostscript, and Ghostscript 10.02 or later
  answers with a deprecation notice on stdout, even with `-v 0`. ManimGL reads the SVG from
  stdout, so the notice ended up at the start of the SVG string.
- **Fix:** keep only the text from the XML declaration (or `<svg`) onward. If dvisvgm produced
  no SVG at all, raise `LatexError`. The `.tex` file is now written explicitly as UTF-8.
- **Note:** a broken SVG may already be in the Tex cache from before the fix. Clear the cache
  (the `directories.latex_cache` folder from your config) if errors persist.

### 2. `TexturedSurface.pointwise_become_partial` referenced an undefined name

- **File:** `manimlib/mobject/types/surface.py`
- **Cause:** the method sliced `im_coords` without ever defining it.
- **Fix:** bind `im_coords = data["im_coords"]` inside the `being_written()` block and do the
  partial slicing there.

### 3. `AnimatedStreamLines.update` had an outdated signature

- **File:** `manimlib/mobject/vector_field.py`
- **Cause:** since #2481 ("Fix time based updaters"), `Scene` calls
  `mobject.update(dt, frame_rate=...)`, and `Mobject.update` forwards `(dt, recurse, frame_rate)`
  to submobjects. `AnimatedStreamLines` overrode `update(self, dt)`.
- **Fix:** match the signature of `Mobject.update` and return `self`, as it does.

### 4. `AnimationGroup` checked an exhausted generator

- **File:** `manimlib/animation/composition.py`
- **Cause:** `AnimationGroup` built `self.animations` from its argument and then ran
  `all(isinstance(anim.mobject, VMobject) for anim in animations)` on that same argument. When
  the argument is a generator, as in `LaggedStart(FadeTransform(a, b) for ...)`, it is already
  exhausted, so `all()` returned `True` and non-VMobjects were put into a `VGroup`.
- **Fix:** check the prepared list `self.animations`.

### 5. `DotCloud.get_radius` crashed on an empty cloud

- **File:** `manimlib/mobject/types/dot_cloud.py`
- **Cause:** `get_radius` returned the max of the per-point radii, which raises `ValueError` for
  a cloud with no points. `compute_bounding_box` calls it, so an empty `DotCloud` or `GlowDots`
  crashed as soon as it was animated.
- **Fix:** fall back to the nominal radius when there are no points.

### 6. `TexturedGeometry` (`ThreeDModel`) could not be drawn

- **File:** `manimlib/mobject/types/surface.py`
- **Cause:** `TexturedGeometry` calls `Mobject.__init__` directly and skips `Surface.__init__`.
  It also registered only a `LightTexture`:
  - `textured_surface.wgsl` always samples `DarkTexture`, so the shader failed to compile;
  - the renderer reads `sort_to_camera` and `opaque_version` every frame, and they were
    missing.
- **Fix:** register the same image as `DarkTexture` and set the attributes `Surface.__init__`
  would have set, including `preferred_creation_axis`.
- **Known limitation (unchanged):** `ShowCreation` of a `ThreeDModel` is still not supported,
  since a model is not a grid. Use `FadeIn` or similar instead.

## Behavior changes in upstream that are not bugs

These upstream changes break older scene code, but they are deliberate, so this fork keeps them
as they are. They are listed here because they look like bugs when running older scenes.

- **#2481, updaters and `dt`:** an updater now receives `dt` whenever its function can take
  two positional arguments. A pattern like `lambda m, i=i: ...` therefore gets `dt` in place of
  `i`. Before, `dt` was passed only to a parameter literally named `dt`.
- **#2482, surface normals:** `Surface` no longer has `d_normal_point`; normals are computed in
  the shader. Use `get_unit_normals()` to read them.
- **`Scene.begin_animations`:** a mobject whose submobjects are all on screen already is not
  added again, so updaters attached to that parent group may never run. Add the group with
  `self.add(group)` before animating it.
