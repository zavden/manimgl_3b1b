# Changes in this fork

This is a fork of [3b1b/manim](https://github.com/3b1b/manim) (ManimGL 1.7.2). It is based on
upstream commit [`fafa083`](https://github.com/3b1b/manim/commit/fafa083a4fb274bba9cabde0b6e2f50ba6da0622)
("Add VideoMobject and Sprite (#2521)", 2026-09-09), and the `fixes` branch adds **bug fixes
only**: no new features and no API changes. Scenes written for upstream ManimGL run the same
way here.

The fixes came up while running the public [3b1b/videos](https://github.com/3b1b/videos) code
on this version. Each one is a single commit whose message explains the cause; the table
names each commit by its title, which `git log` shows next to its hash.

To see every change against upstream:

```bash
git log --reverse fafa083..fixes        # one commit per fix
git diff fafa083 fixes -- manimlib      # the whole code diff
```

## Summary

| # | Area | Symptom on upstream | Commit |
|---|---|---|---|
| 1 | `Tex`, `TexText` | Every LaTeX mobject fails with `ParseError: syntax error: line 1, column 0` | Skip non-SVG text that Ghostscript prints before dvisvgm output |
| 2 | `TexturedSurface` | `NameError: im_coords` on `ShowCreation` and other partial animations | Fix NameError in TexturedSurface.pointwise_become_partial |
| 3 | `AnimatedStreamLines` | `TypeError: unexpected keyword argument 'frame_rate'` on every frame | Let AnimatedStreamLines.update accept recurse and frame_rate |
| 4 | `AnimationGroup`, `LaggedStart` | `Only VMobjects can be passed into VGroup` when given a generator of non-VMobject animations | Decide AnimationGroup's group type from the prepared animations |
| 5 | `DotCloud`, `GlowDots` | `ValueError` (max of an empty array) when an empty cloud is animated | Handle empty DotClouds in DotCloud.get_radius |
| 6 | `TexturedGeometry`, `ThreeDModel` | Shader compile error (`DarkTexture`) and `AttributeError` when drawn | Give TexturedGeometry the attributes and textures it needs to render |
| 7 | `Mobject.set_uniforms` | `AttributeError: 'dict' object has no attribute 'dtype'` when given a dict | Let Mobject.set_uniforms take a dict again |
| 8 | `AnimationGroup`, `LaggedStart` | `IndexError: tuple index out of range` when called with no animations | Let AnimationGroup be built with no arguments |
| 9 | `VMobject.point_from_proportion` | `AssertionError` on a curve of length zero, e.g. `Arc(angle=0).pfp(0.5)` | Return the start from point_from_proportion on zero-length curves |
| 10 | `LaggedStartMap` | `TypeError: Animation only works for Mobjects` when mapping over a list | Let LaggedStartMap map over a list of mobjects again |
| 11 | `Scene` | Memory grows with every mobject ever added, even after it is removed, until the process is killed | Let mobjects removed from a scene be freed |

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

### 7. `Mobject.set_uniforms` no longer accepted a dict

- **File:** `manimlib/mobject/mobject.py`
- **Cause:** until #2482, `set_uniforms` took a dict of uniform names to values. Since then,
  uniforms are a structured array and `set_uniforms` calls `StructuredArray.match`, which
  reads `other.dtype` before anything else. A dict, for example `set_uniforms(dict(m.uniforms))`
  or `set_uniforms({"shading": ...})`, therefore raised `AttributeError`, even though the
  fallback that `match` uses (`StructuredArray.update`) handles mappings.
- **Fix:** pass structured arrays to `match` as before, and plain mappings to `update`, which
  sets the uniforms the mobject has and skips the rest.

### 8. `AnimationGroup()` with no arguments raised IndexError

- **File:** `manimlib/animation/composition.py`
- **Cause:** since "Allow LaggedStart to accept an iterable as an argument" (March 2024),
  `__init__` looks at `args[0]` to tell an iterable from separate animations, without checking
  that there is an `args[0]`. Code that builds a group from a possibly empty list, like
  `LaggedStart(*anims)`, failed when the list was empty, while `AnimationGroup([])` worked.
- **Fix:** check that there are arguments first; with none, the group is empty, as with `[]`.

### 9. `point_from_proportion` failed on curves of length zero

- **File:** `manimlib/mobject/types/vectorized_mobject.py` (`curve_and_prop_of_partial_point`)
- **Cause:** when "Factor out partial results from point_from_proportion" (Sept. 2023) moved the
  search into `curve_and_prop_of_partial_point`, the zero-length case went from returning the
  start point to returning the curve index `len(partials)`, one past the last curve. The
  following `get_nth_curve_function` then failed its `n < get_num_curves()` assertion. This
  happens, for instance, with `Arc(angle=0)` or a `Line` whose ends coincide, often in updaters
  that make an arc from an angle that passes through zero.
- **Fix:** return the start of the first curve, which is what `point_from_proportion` returned
  before.

### 10. `LaggedStartMap` stopped accepting a list of mobjects

- **File:** `manimlib/animation/composition.py`
- **Cause:** "Pass group parameter in LaggedStartMap -> AnimationGroup" (June 2024) made
  `LaggedStartMap` hand its second argument to `AnimationGroup` as `group`, which becomes the
  animation's mobject. Before, any iterable of mobjects worked, as in
  `LaggedStartMap(FadeIn, [a, b, c])` or `LaggedStartMap(ShowCreation, mob.family_members_with_points())`;
  since then a list raises `TypeError` in `Animation.__init__`.
- **Fix:** pass the argument as `group` only when it is a `Mobject`. Otherwise `AnimationGroup`
  builds the group from the animations, as it did before.

### 11. A scene kept every mobject it was ever given

- **File:** `manimlib/scene/scene.py`
- **Cause:** since "Have scene keep track of a map from mobject ids to mobjects for all it's ever
  seen" (April 2022), `Scene.add` stores every member of every family it is given in
  `id_to_mobject_map`, which only serves to paste mobjects by id in the interactive scene. The
  map held them strongly and was never pruned, so nothing that had been on screen could be freed
  before the scene ended. A scene that keeps replacing a large mobject, such as a growing
  paragraph of `Text` redrawn on each step, grew by the whole mobject each time and was killed
  for running out of memory.
- **Fix:** make the map a `weakref.WeakValueDictionary`. Mobjects still referenced anywhere
  (on screen, in a variable, in a group) stay in it as before; the others can be freed, and
  their entries go with them.

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
- **`VectorField` (Nov. 2024, `64ae1364` and follow-ups):** it now takes a `coordinate_system`
  and a `density`. The old keywords (`x_density`, `width`, `height`, `depth`, `sample_points`,
  ...) used by scenes from 2023 and earlier are gone.
- **Scene checkpoints (Dec. 2024, `636fb3a4`):** `Scene.checkpoint` and
  `Scene.revert_to_checkpoint` moved into the interactive embed (`checkpoint_paste`), so a
  `construct` that calls them fails.
- **Surface shader data:** `Surface.get_shader_data` no longer exists; read vertices from
  `get_points()` instead.
- **`Mobject.uniforms`:** it is a structured array now, not a dict. Reading and writing single
  entries (`mob.uniforms["shading"]`) works as before.
- **`FillArrow` (Aug. 2024, `22ee13a8`):** it took the place of `Arrow`, and the name `FillArrow`
  no longer exists.
- **`set_stroke(background=...)` (Aug. 2024, `74878037`):** renamed to `set_stroke(behind=...)`,
  and `set_style(stroke_background=...)` to `set_style(stroke_behind=...)`, with no alias.
- **`Mobject.update` and subclasses (#2481):** `Scene` now calls `mobject.update(dt, frame_rate=...)`
  and `Mobject.update` passes `frame_rate` to submobjects as a third argument. A subclass that
  overrides `update(self, dt=0, recurse=True)` raises `TypeError`; add a `frame_rate=None`
  parameter.
- **SVG subpath order (Jan. 2023, `ab57b0ac`):** `SVGMobject` reverses each path whose orientation
  comes out negative after the y flip, which is most of them. Reversing the points also reverses
  the order of the subpaths, so `path.get_subpaths()` usually lists the subpaths of a `<path>`
  last to first. Scenes written since then rely on that order.
