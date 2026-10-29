# Changes from upstream commit `fafa083`

This fork is based on [3b1b/manim](https://github.com/3b1b/manim) at commit
[`fafa083`](https://github.com/3b1b/manim/commit/fafa083a4fb274bba9cabde0b6e2f50ba6da0622)
("Add VideoMobject and Sprite (#2521)", 2026-09-09). The `main` branch adds the commits below.
[`FORK_CHANGES.md`](FORK_CHANGES.md) explains each fix (symptom, cause and test); this file lists
every commit and the full code diff.

## Commits (39)

| # | Commit | Date | Title |
|---|---|---|---|
| 1 | `4d1ee1e8` | 2026-10-22 | Skip non-SVG text that Ghostscript prints before dvisvgm output |
| 2 | `888b597a` | 2026-10-22 | Fix NameError in TexturedSurface.pointwise_become_partial |
| 3 | `1aae293e` | 2026-10-22 | Let AnimatedStreamLines.update accept recurse and frame_rate |
| 4 | `7abd327c` | 2026-10-22 | Decide AnimationGroup's group type from the prepared animations |
| 5 | `0cf2b238` | 2026-10-22 | Handle empty DotClouds in DotCloud.get_radius |
| 6 | `d433356a` | 2026-10-22 | Give TexturedGeometry the attributes and textures it needs to render |
| 7 | `331e8551` | 2026-10-22 | Document the fork's changes in FORK_CHANGES.md |
| 8 | `c0651591` | 2026-10-22 | Let Mobject.set_uniforms take a dict again |
| 9 | `ec1b2011` | 2026-10-22 | List more upstream API changes that break older scenes |
| 10 | `5e85eb97` | 2026-10-22 | Let AnimationGroup be built with no arguments |
| 11 | `21c3cf41` | 2026-10-22 | Return the start from point_from_proportion on zero-length curves |
| 12 | `bfcaf8f8` | 2026-10-22 | Document the set_stroke rename and the new Mobject.update signature |
| 13 | `8a7fb4e6` | 2026-10-22 | Document that FillArrow became Arrow |
| 14 | `15a9c921` | 2026-10-23 | Let LaggedStartMap map over a list of mobjects again |
| 15 | `1a5a2647` | 2026-10-23 | Let mobjects removed from a scene be freed |
| 16 | `c25480af` | 2026-10-23 | Bound the cache of mobjects built from SVGs |
| 17 | `8fedca46` | 2026-10-23 | Document that coordinate system ranges always hold a step |
| 18 | `f0544a10` | 2026-10-23 | Let an animation blend a mobject whose point count changed |
| 19 | `9fea0b5f` | 2026-10-23 | Have get_y_unit_size measure the y axis |
| 20 | `a104b1e5` | 2026-10-23 | Document more upstream API changes that break older scenes |
| 21 | `f8f61df1` | 2026-10-23 | Hand OldTex the svg it is drawn from |
| 22 | `450d091f` | 2026-10-23 | Index StringMobjects by numpy integers too |
| 23 | `99997b8a` | 2026-10-23 | Balance OldTex braces without shifting the string being read |
| 24 | `f6bf6aa2` | 2026-10-23 | Color a NewtonFractal of zero steps by the nearest root |
| 25 | `83cc4c83` | 2026-10-23 | Keep the usual color for background lines styled without one |
| 26 | `564c51da` | 2026-10-23 | Let Sector take outer_radius, as its docstring says |
| 27 | `385d4cf9` | 2026-10-23 | Treat alphas past one as one in Animation.interpolate |
| 28 | `9470a0d0` | 2026-10-23 | Keep a deep copied StructuredArray's views on its own array |
| 29 | `58245596` | 2026-10-23 | Document float32 points and the Arrow thickness scale as upstream changes |
| 30 | `985c7586` | 2026-10-23 | Refresh bounding boxes after Rotating resets its points |
| 31 | `00ab6c9a` | 2026-10-26 | Return the mobject from Mobject.fade |
| 32 | `cddf90cd` | 2026-10-26 | Let Axes take unit_size inside axis_config |
| 33 | `72f13b37` | 2026-10-26 | Format a complex DecimalNumber with no decimals |
| 34 | `f942b8a8` | 2026-10-26 | Let FadeInFromPoint and FadeOutToPoint take a Mobject as the point |
| 35 | `56bcfdbd` | 2026-10-26 | Write each markup SVG to a temporary file of its own |
| 36 | `3e3445d3` | 2026-10-28 | Explain in the README what this fork is for |
| 37 | `3e182292` | 2026-10-28 | Add DIFF.md with every change against upstream |
| 38 | `0d061b24` | 2026-10-28 | Update README.md |
| 39 | `71828ff1` | 2026-10-28 | Use the manimgl_3b1b name and link the companion repositories |

## Files changed

```
 DIFF.md                                      | 652 +++++++++++++++++++++++++++++++++++++++++++++++
 FORK_CHANGES.md                              | 383 ++++++++++++++++++++++++++++
 README.md                                    |  48 +++-
 manimlib/animation/animation.py              |   4 +-
 manimlib/animation/composition.py            |  11 +-
 manimlib/animation/fading.py                 |  11 +-
 manimlib/animation/rotation.py               |   3 +
 manimlib/mobject/coordinate_systems.py       |   9 +-
 manimlib/mobject/geometry.py                 |   4 +-
 manimlib/mobject/mobject.py                  |  14 +-
 manimlib/mobject/numbers.py                  |   8 +-
 manimlib/mobject/svg/old_tex_mobject.py      |  15 +-
 manimlib/mobject/svg/string_mobject.py       |   4 +-
 manimlib/mobject/svg/svg_mobject.py          |  10 +-
 manimlib/mobject/svg/text_mobject.py         |   8 +-
 manimlib/mobject/types/dot_cloud.py          |   4 +-
 manimlib/mobject/types/surface.py            |  27 +-
 manimlib/mobject/types/vectorized_mobject.py |   3 +-
 manimlib/mobject/vector_field.py             |  11 +-
 manimlib/scene/scene.py                      |   5 +-
 manimlib/shaders/newton_fractal.wgsl         |   4 +-
 manimlib/utils/structured_array.py           |   7 +
 manimlib/utils/tex_file_writing.py           |   8 +-
 23 files changed, 1202 insertions(+), 51 deletions(-)
```

## Full diff of `manimlib/`

```diff
diff --git a/manimlib/animation/animation.py b/manimlib/animation/animation.py
index 02997711..a5116971 100644
--- a/manimlib/animation/animation.py
+++ b/manimlib/animation/animation.py
@@ -164,7 +164,9 @@ class Animation(object):
 
     # Methods for interpolation, the mean of an Animation
     def interpolate(self, alpha: float) -> None:
-        self.interpolate_mobject(alpha)
+        # A play lasts as long as its longest animation, and the others are handed alphas past
+        # one once they are done, which they should treat as one
+        self.interpolate_mobject(clip(alpha, 0, 1))
 
     def update(self, alpha: float) -> None:
         """
diff --git a/manimlib/animation/composition.py b/manimlib/animation/composition.py
index 49859fd3..3b659aa1 100644
--- a/manimlib/animation/composition.py
+++ b/manimlib/animation/composition.py
@@ -4,6 +4,7 @@ from manimlib.animation.animation import Animation
 from manimlib.animation.animation import prepare_animation
 from manimlib.mobject.mobject import _AnimationBuilder
 from manimlib.mobject.mobject import Group
+from manimlib.mobject.mobject import Mobject
 from manimlib.mobject.types.vectorized_mobject import VGroup
 from manimlib.mobject.types.vectorized_mobject import VMobject
 from manimlib.utils.bezier import integer_interpolate
@@ -17,7 +18,6 @@ AnimationType = Union[Animation, _AnimationBuilder]
 if TYPE_CHECKING:
     from typing import Callable, Optional
 
-    from manimlib.mobject.mobject import Mobject
     from manimlib.scene.scene import Scene
 
 
@@ -35,7 +35,8 @@ class AnimationGroup(Animation):
         time_span: tuple[float, float] | None = None,
         **kwargs
     ):
-        animations = args[0] if isinstance(args[0], Iterable) else args
+        # With no arguments at all this is an empty group, like AnimationGroup([])
+        animations = args[0] if args and isinstance(args[0], Iterable) else args
         self.animations = [prepare_animation(anim) for anim in animations]
         self.build_animations_with_timings(lag_ratio)
         self.max_end_time = max((awt[2] for awt in self.anims_with_timings), default=0)
@@ -51,7 +52,7 @@ class AnimationGroup(Animation):
             self.group = group
         elif group_type is not None:
             self.group = group_type(*mobs)
-        elif all(isinstance(anim.mobject, VMobject) for anim in animations):
+        elif all(isinstance(anim.mobject, VMobject) for anim in self.animations):
             self.group = VGroup(*mobs)
         else:
             self.group = Group(*mobs)
@@ -182,6 +183,8 @@ class LaggedStartMap(LaggedStart):
             *(anim_func(submob, **anim_kwargs) for submob in group),
             run_time=run_time,
             lag_ratio=lag_ratio,
-            group=group,
+            # The group may also be a plain list of mobjects, which can't serve as the
+            # animation's mobject, so then one is built from the animations instead
+            group=group if isinstance(group, Mobject) else None,
             time_span=time_span,
         )
diff --git a/manimlib/animation/fading.py b/manimlib/animation/fading.py
index 0168a7a1..b00efdf4 100644
--- a/manimlib/animation/fading.py
+++ b/manimlib/animation/fading.py
@@ -6,7 +6,7 @@ from manimlib.animation.animation import Animation
 from manimlib.animation.transform import Transform
 from manimlib.constants import ORIGIN
 from manimlib.mobject.types.vectorized_mobject import VMobject
-from manimlib.mobject.mobject import Group
+from manimlib.mobject.mobject import Group, Mobject
 from manimlib.utils.bezier import interpolate
 from manimlib.utils.rate_functions import there_and_back
 
@@ -14,7 +14,6 @@ from typing import TYPE_CHECKING
 
 if TYPE_CHECKING:
     from typing import Callable
-    from manimlib.mobject.mobject import Mobject
     from manimlib.scene.scene import Scene
     from manimlib.typing import Vect3
 
@@ -69,7 +68,9 @@ class FadeOut(Fade):
 
 
 class FadeInFromPoint(FadeIn):
-    def __init__(self, mobject: Mobject, point: Vect3, **kwargs):
+    def __init__(self, mobject: Mobject, point: Vect3 | Mobject, **kwargs):
+        if isinstance(point, Mobject):
+            point = point.get_center()
         super().__init__(
             mobject,
             shift=mobject.get_center() - point,
@@ -79,7 +80,9 @@ class FadeInFromPoint(FadeIn):
 
 
 class FadeOutToPoint(FadeOut):
-    def __init__(self, mobject: Mobject, point: Vect3, **kwargs):
+    def __init__(self, mobject: Mobject, point: Vect3 | Mobject, **kwargs):
+        if isinstance(point, Mobject):
+            point = point.get_center()
         super().__init__(
             mobject,
             shift=point - mobject.get_center(),
diff --git a/manimlib/animation/rotation.py b/manimlib/animation/rotation.py
index 4c001fdb..f4cfbc1f 100644
--- a/manimlib/animation/rotation.py
+++ b/manimlib/animation/rotation.py
@@ -47,6 +47,9 @@ class Rotating(Animation):
         for sm1, sm2 in pairs:
             for key in sm1.pointlike_data_keys:
                 sm1.data[key] = sm2.data[key]
+        # Written straight into the data, which leaves the bounding boxes as they were after the
+        # last frame's rotation, and the rotation below finds its center from them
+        self.mobject.refresh_bounding_box(recurse_down=True)
         self.mobject.rotate(
             self.rate_func(self.time_spanned_alpha(alpha)) * self.angle,
             axis=self.axis,
diff --git a/manimlib/mobject/coordinate_systems.py b/manimlib/mobject/coordinate_systems.py
index b7f0ba11..5750b841 100644
--- a/manimlib/mobject/coordinate_systems.py
+++ b/manimlib/mobject/coordinate_systems.py
@@ -459,7 +459,7 @@ class Axes(VGroup, CoordinateSystem):
         kwargs.pop("num_sampled_graph_points_per_tick", None)
         VGroup.__init__(self, **kwargs)
 
-        axis_config = dict(**axis_config, unit_size=unit_size)
+        axis_config = {"unit_size": unit_size, **axis_config}
         self.x_axis = self.create_axis(
             self.x_range,
             axis_config=merge_dicts_recursively(
@@ -659,6 +659,9 @@ class NumberPlane(Axes):
     ):
         super().__init__(x_range, y_range, **kwargs)
         self.background_line_style = dict(background_line_style)
+        # A style given without a color, say only a width, keeps the usual one rather than
+        # leaving the faded lines below without one to copy
+        self.background_line_style.setdefault("stroke_color", BLUE_D)
         self.faded_line_style = dict(faded_line_style)
         self.faded_line_ratio = faded_line_ratio
         self.make_smooth_after_applying_functions = make_smooth_after_applying_functions
@@ -714,8 +717,8 @@ class NumberPlane(Axes):
     def get_x_unit_size(self) -> float:
         return self.get_x_axis().get_unit_size()
 
-    def get_y_unit_size(self) -> list:
-        return self.get_x_axis().get_unit_size()
+    def get_y_unit_size(self) -> float:
+        return self.get_y_axis().get_unit_size()
 
     def get_axes(self) -> VGroup:
         return self.axes
diff --git a/manimlib/mobject/geometry.py b/manimlib/mobject/geometry.py
index f9beb34c..83c4de32 100644
--- a/manimlib/mobject/geometry.py
+++ b/manimlib/mobject/geometry.py
@@ -597,12 +597,14 @@ class Sector(AnnularSector):
         self,
         angle: float = TAU / 4,
         radius: float = 1.0,
+        # The name the docstring above uses, as AnnularSector does
+        outer_radius: float | None = None,
         **kwargs
     ):
         super().__init__(
             angle,
             inner_radius=0,
-            outer_radius=radius,
+            outer_radius=radius if outer_radius is None else outer_radius,
             **kwargs
         )
 
diff --git a/manimlib/mobject/mobject.py b/manimlib/mobject/mobject.py
index 625d5eb3..5955e2bc 100644
--- a/manimlib/mobject/mobject.py
+++ b/manimlib/mobject/mobject.py
@@ -168,8 +168,12 @@ class Mobject(object):
         # Typically implemented in subclass, unlpess purposefully left blank
         pass
 
-    def set_uniforms(self, uniforms: Uniforms) -> Self:
-        self.uniforms.match(uniforms)
+    def set_uniforms(self, uniforms: Uniforms | dict) -> Self:
+        if isinstance(uniforms, StructuredArray):
+            self.uniforms.match(uniforms)
+        else:
+            # A plain mapping of uniform names to values, as this method used to take
+            self.uniforms.update(uniforms)
         return self
 
     @property
@@ -1438,6 +1442,7 @@ class Mobject(object):
 
     def fade(self, darkness: float = 0.5, recurse: bool = True) -> Self:
         self.set_opacity(1.0 - darkness, recurse=recurse)
+        return self
 
     def get_shading(self) -> np.ndarray:
         return self.uniforms["shading"]
@@ -1832,6 +1837,11 @@ class Mobject(object):
                 for key in self.pointlike_uniform_keys
             }
 
+        if len(self.data) != len(mobject1.data):
+            # Something other than the animation, an updater say, changed how many points this
+            # holds after the animation began, as DecimalNumber.set_value does in place. The
+            # blend settles what it holds then, as it does whenever the count is unchanged
+            self.resize_points(len(mobject1.data))
         self.data.interpolate(mobject1.data, mobject2.data, alpha, data_keys_to_alt_func)
         self.uniforms.interpolate(
             mobject1.uniforms, mobject2.uniforms, alpha, uniform_keys_to_alt_func,
diff --git a/manimlib/mobject/numbers.py b/manimlib/mobject/numbers.py
index 288ff736..f79e0b67 100644
--- a/manimlib/mobject/numbers.py
+++ b/manimlib/mobject/numbers.py
@@ -1,5 +1,6 @@
 from __future__ import annotations
 from functools import lru_cache
+from types import SimpleNamespace
 
 import numpy as np
 
@@ -135,7 +136,12 @@ class DecimalNumber(VMobject):
             formatter = self.get_formatter()
         if self.num_decimal_places == 0 and isinstance(number, float):
             number = int(number)
-        num_string = formatter.format(number)
+        if self.num_decimal_places == 0 and isinstance(number, complex):
+            # "d" cannot format the float parts of a complex, so hand the formatter integer parts
+            parts = SimpleNamespace(real=int(round(number.real)), imag=int(round(number.imag)))
+            num_string = formatter.format(parts)
+        else:
+            num_string = formatter.format(number)
 
         rounded_num = np.round(number, self.num_decimal_places)
         if num_string.startswith("-") and rounded_num == 0:
diff --git a/manimlib/mobject/svg/old_tex_mobject.py b/manimlib/mobject/svg/old_tex_mobject.py
index bef9aff6..aa74d278 100644
--- a/manimlib/mobject/svg/old_tex_mobject.py
+++ b/manimlib/mobject/svg/old_tex_mobject.py
@@ -48,6 +48,9 @@ class SingleStringTex(SVGMobject):
         self.additional_preamble = additional_preamble
 
         super().__init__(
+            # Since SVGMobject came to take the svg itself rather than a file to find it in,
+            # this has to be handed the svg, it no longer being asked for a file
+            svg_string=self.get_svg_string_by_content(self.get_tex_file_body(tex_string)),
             height=height,
             fill_color=fill_color,
             fill_opacity=fill_opacity,
@@ -75,7 +78,7 @@ class SingleStringTex(SVGMobject):
         )
 
     def get_svg_string_by_content(self, content: str) -> str:
-        return latex_to_svg(content, self.template, self.additional_preamble)
+        return latex_to_svg(content, self.template, self.additional_preamble, short_tex=self.tex_string)
 
     def get_tex_file_body(self, tex_string: str) -> str:
         new_tex = self.get_modified_expression(tex_string)
@@ -155,6 +158,9 @@ class SingleStringTex(SVGMobject):
         Makes Tex resiliant to unmatched braces
         """
         num_unclosed_brackets = 0
+        # Counted rather than added as they are found, since adding one to the front
+        # while going through the string moves everything after it along
+        num_unopened_brackets = 0
         for i in range(len(tex)):
             if i > 0 and tex[i - 1] == "\\":
                 # So as to not count '\{' type expressions
@@ -164,11 +170,10 @@ class SingleStringTex(SVGMobject):
                 num_unclosed_brackets += 1
             elif char == "}":
                 if num_unclosed_brackets == 0:
-                    tex = "{" + tex
+                    num_unopened_brackets += 1
                 else:
                     num_unclosed_brackets -= 1
-        tex += num_unclosed_brackets * "}"
-        return tex
+        return num_unopened_brackets * "{" + tex + num_unclosed_brackets * "}"
 
     def get_tex(self) -> str:
         return self.tex_string
@@ -310,7 +315,7 @@ class OldTex(SingleStringTex):
         self.submobjects.sort(key=lambda m: m.get_tex())
 
     def set_bstroke(self, color: ManimColor = BLACK, width: float = 4):
-        self.set_stroke(color, width, background=True)
+        self.set_stroke(color, width, behind=True)
         return self
 
 
diff --git a/manimlib/mobject/svg/string_mobject.py b/manimlib/mobject/svg/string_mobject.py
index be90154f..5e33d8a8 100644
--- a/manimlib/mobject/svg/string_mobject.py
+++ b/manimlib/mobject/svg/string_mobject.py
@@ -2,6 +2,7 @@ from __future__ import annotations
 
 from abc import ABC, abstractmethod
 import itertools as it
+import numbers
 import re
 from scipy.optimize import linear_sum_assignment
 from scipy.spatial.distance import cdist
@@ -551,7 +552,8 @@ class StringMobject(SVGMobject, ABC):
         return self.build_parts_from_indices_lists(indices_list)
 
     def __getitem__(self, value: int | slice | Selector) -> VMobject:
-        if isinstance(value, (int, slice)):
+        # Integral rather than int, so that an index numpy gives, as from argmin, is one
+        if isinstance(value, (numbers.Integral, slice)):
             return super().__getitem__(value)
         return self.select_parts(value)
 
diff --git a/manimlib/mobject/svg/svg_mobject.py b/manimlib/mobject/svg/svg_mobject.py
index 0cc1822e..1601b230 100644
--- a/manimlib/mobject/svg/svg_mobject.py
+++ b/manimlib/mobject/svg/svg_mobject.py
@@ -1,5 +1,6 @@
 from __future__ import annotations
 
+from collections import OrderedDict
 from xml.etree import ElementTree as ET
 
 import numpy as np
@@ -31,7 +32,10 @@ if TYPE_CHECKING:
 # STROKE_WIDTH_CONVERSION in shaders/stroke.wgsl
 STROKE_WIDTHS_PER_UNIT: float = 100.0
 
-SVG_HASH_TO_MOB_MAP: dict[int, list[VMobject]] = {}
+# Most recently used last. It is bounded by how many mobjects it holds in all, since a scene
+# whose text keeps changing would otherwise keep every version of it until the program ends
+SVG_HASH_TO_MOB_MAP: OrderedDict[int, list[VMobject]] = OrderedDict()
+SVG_CACHE_MAX_SUBMOBJECTS: int = 10000
 PATH_TO_POINTS: dict[str, Vect3Array] = {}
 
 
@@ -147,10 +151,14 @@ class SVGMobject(VMobject):
     def init_svg_mobject(self) -> None:
         hash_val = hash_obj(self.hash_seed)
         if hash_val in SVG_HASH_TO_MOB_MAP:
+            SVG_HASH_TO_MOB_MAP.move_to_end(hash_val)
             submobs = [sm.copy() for sm in SVG_HASH_TO_MOB_MAP[hash_val]]
         else:
             submobs = self.mobjects_from_svg_string(self.svg_string)
             SVG_HASH_TO_MOB_MAP[hash_val] = [sm.copy() for sm in submobs]
+            cached = sum(map(len, SVG_HASH_TO_MOB_MAP.values()))
+            while cached > SVG_CACHE_MAX_SUBMOBJECTS and len(SVG_HASH_TO_MOB_MAP) > 1:
+                cached -= len(SVG_HASH_TO_MOB_MAP.popitem(last=False)[1])
 
         self.add(*submobs)
         self.flip(RIGHT)  # Flip y
diff --git a/manimlib/mobject/svg/text_mobject.py b/manimlib/mobject/svg/text_mobject.py
index 91bfada8..2eb4bfd8 100644
--- a/manimlib/mobject/svg/text_mobject.py
+++ b/manimlib/mobject/svg/text_mobject.py
@@ -21,7 +21,6 @@ from manimlib.mobject.svg.svg_mobject import get_svg_content_height
 from manimlib.utils.cache import cache_on_disk
 from manimlib.utils.color import color_to_hex
 from manimlib.utils.color import int_to_hex
-from manimlib.utils.simple_functions import hash_string
 
 from typing import TYPE_CHECKING
 
@@ -74,8 +73,11 @@ def markup_to_svg(
     else:
         pango_width = line_width / FRAME_WIDTH * DEFAULT_PIXEL_WIDTH
 
-    # Write the result to a temporary svg file, and return it's contents.
-    temp_file = Path(tempfile.gettempdir(), hash_string(markup_str)).with_suffix(".svg")
+    # Write the result to a temporary svg file of its own (one named after the markup would be
+    # shared by renders running at the same time), and return it's contents.
+    fd, temp_name = tempfile.mkstemp(suffix=".svg")
+    os.close(fd)
+    temp_file = Path(temp_name)
     manimpango.MarkupUtils.text2svg(
         text=markup_str,
         font="",                     # Already handled
diff --git a/manimlib/mobject/types/dot_cloud.py b/manimlib/mobject/types/dot_cloud.py
index 2bac3081..e7e54d66 100644
--- a/manimlib/mobject/types/dot_cloud.py
+++ b/manimlib/mobject/types/dot_cloud.py
@@ -120,7 +120,9 @@ class DotCloud(PMobject):
         return self
 
     def get_radius(self) -> float:
-        return self.get_radii().max()
+        radii = self.get_radii()
+        # An empty cloud has no radii, but its bounding box still asks for one
+        return radii.max() if len(radii) > 0 else self.radius
 
     def scale_radii(self, scale_factor: float) -> Self:
         self.set_radius(scale_factor * self.get_radii())
diff --git a/manimlib/mobject/types/surface.py b/manimlib/mobject/types/surface.py
index a0b3dfce..3dae48d9 100644
--- a/manimlib/mobject/types/surface.py
+++ b/manimlib/mobject/types/surface.py
@@ -491,13 +491,14 @@ class TexturedSurface(Surface):
             axis = self.preferred_creation_axis
         super().pointwise_become_partial(tsmobject, a, b, axis)
         with self.data.being_written() as data:
-            data["im_coords"][:] = tsmobject.data["im_coords"]
-        if a <= 0 and b >= 1:
-            return self
-        nu, nv = tsmobject.get_resolution()
-        im_coords[:] = self.get_partial_points_array(
-            im_coords, a, b, (nu, nv, 2), axis
-        )
+            im_coords = data["im_coords"]
+            im_coords[:] = tsmobject.data["im_coords"]
+            if a <= 0 and b >= 1:
+                return self
+            nu, nv = tsmobject.get_resolution()
+            im_coords[:] = self.get_partial_points_array(
+                im_coords, a, b, (nu, nv, 2), axis
+            )
         return self
 
 
@@ -516,9 +517,19 @@ class TexturedGeometry(TexturedSurface):
         self.texture_file = texture_file
         # Not a grid, which is what the vertex shader goes by, see surface_mesh.wgsl
         self.initial_resolution = (0, 0)
+        # Surface.__init__ is skipped, so set what it would have, since the renderer
+        # and partial animations read these
+        self.preferred_creation_axis = 1
+        self.sort_to_camera = False
+        self.opaque_version = 0
+        image_path = get_full_raster_image_path(texture_file)
         Mobject.__init__(
             self,
-            textures={"LightTexture": ImageFile(get_full_raster_image_path(texture_file))}
+            # textured_surface.wgsl always samples a DarkTexture, so give it the same image
+            textures={
+                "LightTexture": ImageFile(image_path),
+                "DarkTexture": ImageFile(image_path),
+            }
         )
 
     def init_points(self):
diff --git a/manimlib/mobject/types/vectorized_mobject.py b/manimlib/mobject/types/vectorized_mobject.py
index fc99bcd3..4604f939 100644
--- a/manimlib/mobject/types/vectorized_mobject.py
+++ b/manimlib/mobject/types/vectorized_mobject.py
@@ -1034,7 +1034,8 @@ class VMobject(Mobject):
             partials.append(partials[-1] + arclen)
         full = partials[-1]
         if full == 0:
-            return len(partials), 1.0
+            # Every point coincides, so the start of the first curve is as good as any
+            return 0, 0.0
         # First index where the partial length is more than alpha times the full length
         index = next(
             (i for i, x in enumerate(partials) if x >= full * alpha),
diff --git a/manimlib/mobject/vector_field.py b/manimlib/mobject/vector_field.py
index 88cb9a36..28f924e3 100644
--- a/manimlib/mobject/vector_field.py
+++ b/manimlib/mobject/vector_field.py
@@ -22,7 +22,7 @@ from typing import TYPE_CHECKING
 
 if TYPE_CHECKING:
     from typing import Callable, Iterable, Sequence, TypeVar, Tuple, Optional
-    from manimlib.typing import ManimColor, Vect3, VectN, VectArray, Vect3Array, Vect4Array
+    from manimlib.typing import ManimColor, Vect3, VectN, VectArray, Vect3Array, Vect4Array, Self
 
     from manimlib.mobject.coordinate_systems import CoordinateSystem
     from manimlib.mobject.mobject import Mobject
@@ -469,9 +469,16 @@ class AnimatedStreamLines(VGroup):
 
         self.add_updater(lambda m, dt: m.update(dt))
 
-    def update(self, dt: float = 0) -> None:
+    def update(
+        self,
+        dt: float = 0,
+        recurse: bool = True,
+        frame_rate: float | None = None
+    ) -> Self:
+        # Same signature as Mobject.update, which Scene calls with frame_rate
         stream_lines = self.stream_lines
         for line in stream_lines:
             line.time += dt
             adjusted_time = max(line.time, 0) % line.anim.run_time
             line.anim.update(adjusted_time / line.anim.run_time)
+        return self
diff --git a/manimlib/scene/scene.py b/manimlib/scene/scene.py
index 2c9b9809..6146ac35 100644
--- a/manimlib/scene/scene.py
+++ b/manimlib/scene/scene.py
@@ -4,6 +4,7 @@ from collections import OrderedDict
 import platform
 import random
 import time
+import weakref
 from functools import wraps
 from contextlib import contextmanager
 from contextlib import ExitStack
@@ -117,7 +118,9 @@ class Scene(object):
 
         self.file_writer = SceneFileWriter(self, **self.file_writer_config)
         self.mobjects: list[Mobject] = [self.camera.frame]
-        self.id_to_mobject_map: dict[int, Mobject] = dict()
+        # Weak, so that a mobject taken out of the scene can still be freed: holding every
+        # mobject ever added kept all of them alive until the scene ended
+        self.id_to_mobject_map: weakref.WeakValueDictionary[int, Mobject] = weakref.WeakValueDictionary()
         self.num_plays: int = 0
         self.time: float = 0
         self.skip_time: float = 0
diff --git a/manimlib/shaders/newton_fractal.wgsl b/manimlib/shaders/newton_fractal.wgsl
index c74a09c5..2e697a95 100644
--- a/manimlib/shaders/newton_fractal.wgsl
+++ b/manimlib/shaders/newton_fractal.wgsl
@@ -107,7 +107,9 @@ struct Search {
 fn seek_root(start: vec2f, coefs: Coefs, max_steps: i32) -> Search {
     var result: Search;
     result.root = start;
-    var step_size = 0.0;
+    // As if the start were far from settled, which only matters when no step is taken at all:
+    // with n_steps of zero the pixel is colored by the root nearest where it started
+    var step_size = 1.0;
     for (var i = 0; i < max_steps; i++) {
         result.steps_taken = f32(i);
         let step = newton_step(result.root, coefs);
diff --git a/manimlib/utils/structured_array.py b/manimlib/utils/structured_array.py
index d7ddee6e..e9c5fde9 100644
--- a/manimlib/utils/structured_array.py
+++ b/manimlib/utils/structured_array.py
@@ -234,6 +234,13 @@ class StructuredArray(object):
 
         self.note_change()
 
+    def __deepcopy__(self, memo: dict) -> Self:
+        # Not left to copy.deepcopy, which would copy the floats and bytes views apart from the
+        # array they look onto, so that writes to the array no longer reach what is drawn or blended
+        result = self.copy()
+        memo[id(self)] = result
+        return result
+
     def copy(self) -> Self:
         result = copy.copy(self)
         result.set_array(self.array.copy())
diff --git a/manimlib/utils/tex_file_writing.py b/manimlib/utils/tex_file_writing.py
index ebf971f4..75aa1f3f 100644
--- a/manimlib/utils/tex_file_writing.py
+++ b/manimlib/utils/tex_file_writing.py
@@ -107,7 +107,7 @@ def full_tex_to_svg(full_tex: str, compiler: str = "latex", message: str = ""):
     dvi_path = tex_path.with_suffix(dvi_ext)
 
     # Write tex file
-    tex_path.write_text(full_tex)
+    tex_path.write_text(full_tex, encoding="utf-8")
 
     # Run latex compiler
     process = subprocess.run(
@@ -151,8 +151,12 @@ def full_tex_to_svg(full_tex: str, compiler: str = "latex", message: str = ""):
         error_str = process.stderr.decode("utf-8", errors="replace").strip()
         raise LatexError(error_str or "dvisvgm failed to convert DVI to SVG")
 
-    # Return SVG string
+    # Return SVG string, skipping any text Ghostscript may print to stdout before it
     result = process.stdout.decode("utf-8")
+    svg_match = re.search(r"<\?xml|<svg", result)
+    if svg_match is None:
+        raise LatexError("dvisvgm did not produce SVG output")
+    result = result[svg_match.start():]
 
     if message:
         print(" " * len(message), end="\r")
```
