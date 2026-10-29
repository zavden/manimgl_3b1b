<p align="center">
    <a href="https://github.com/3b1b/manim">
        <img src="https://raw.githubusercontent.com/3b1b/manim/master/logo/cropped.png">
    </a>
</p>

[![pypi version](https://img.shields.io/pypi/v/manimgl?logo=pypi)](https://pypi.org/project/manimgl/)
[![MIT License](https://img.shields.io/badge/license-MIT-blue.svg?style=flat)](http://choosealicense.com/licenses/mit/)
[![Manim Subreddit](https://img.shields.io/reddit/subreddit-subscribers/manim.svg?color=ff4301&label=reddit&logo=reddit)](https://www.reddit.com/r/manim/)
[![Manim Discord](https://img.shields.io/discord/581738731934056449.svg?label=discord&logo=discord)](https://discord.com/invite/bYCyhM9Kz2)
[![docs](https://github.com/3b1b/manim/workflows/docs/badge.svg)](https://3b1b.github.io/manim/)

> [!Important]
> **This is an unofficial fork of [3b1b/manim](https://github.com/3b1b/manim), not the original project.**
> It is not affiliated with 3Blue1Brown nor endorsed by them, and it is not meant to replace upstream.

## About this fork

This repository exists for one purpose: to run the public [3b1b/videos](https://github.com/3b1b/videos) code on a
recent ManimGL (1.7.2) without it breaking. The scenes were converted and rendered in
[`animations_3b1b`](https://github.com/zavden/animations_3b1b) and needed a handful of engine bug fixes that upstream does not have.

- **Base:** upstream commit [`fafa083`](https://github.com/3b1b/manim/commit/fafa083a4fb274bba9cabde0b6e2f50ba6da0622)
  ("Add VideoMobject and Sprite (#2521)", 2026-09-09). The full upstream history is kept.
- **What it adds:** bug fixes only. No new features, no API changes: a scene written for upstream ManimGL runs the same way here.
- **What every fix is:** [`FORK_CHANGES.md`](FORK_CHANGES.md) gives the symptom, cause and test of each one, and
  [`DIFF.md`](DIFF.md) lists every commit and the full code diff against upstream.
- **Not kept up to date:** this fork does not follow upstream. If you do not need these fixes, install the
  original (`pip install manimgl` from PyPI, [3b1b/manim](https://github.com/3b1b/manim)). Anything that is not a fix belongs upstream.
- **Package name:** it is still `manimgl` (module `manimlib`), so installing it replaces the PyPI one.

Companion repositories:

| Repository | What it has |
|---|---|
| `manimgl_3b1b` (this one) | The ManimGL fork with the fixes |
| [`animations_3b1b`](https://github.com/zavden/animations_3b1b) | The scenes of 3b1b/videos (2017-2026) converted to run on this fork, with their web pages |
| [`assets_3b1b`](https://github.com/zavden/assets_3b1b) | The images, SVGs, sounds and fonts those scenes read, with their licenses |
| [Rendered videos](https://drive.google.com/drive/folders/1BCGrsZvolya2Jwur21eW9Irj0JhkhujV?usp=sharing) | The scenes rendered at 720p, 15 fps (Google Drive) |

The rest of this README is upstream's, with the clone commands pointing to this fork.

Manim is an engine for precise programmatic animations, designed for creating explanatory math videos.

Note, there are two versions of manim.  This repository began as a personal project by the author of [3Blue1Brown](https://www.3blue1brown.com/) for the purpose of animating those videos, with video-specific code available [here](https://github.com/3b1b/videos).  In 2020 a group of developers forked it into what is now the [community edition](https://github.com/ManimCommunity/manim/), with a goal of being more stable, better tested, quicker to respond to community contributions, and all around friendlier to get started with. See [this page](https://docs.manim.community/en/stable/faq/installation.html#different-versions) for more details.

## Installation
> [!Warning]
> **WARNING:** These instructions are for ManimGL _only_. Trying to use these instructions to install [Manim Community/manim](https://github.com/ManimCommunity/manim) or instructions there to install this version will cause problems. You should first decide which version you wish to install, then only follow the instructions for your desired version.

> [!Note]
> **Note**: To install manim directly through pip, please pay attention to the name of the installed package. This repository is ManimGL of 3b1b. The package name is `manimgl` instead of `manim` or `manimlib`. `pip install manimgl` installs the **upstream** release from PyPI, not this fork: to install the fork, use `pip install git+https://github.com/zavden/manimgl_3b1b.git` or clone this repository and run `pip install -e .`.

Manim runs on Python 3.10 or higher.

System requirements are [FFmpeg](https://ffmpeg.org/), [OpenGL](https://www.opengl.org/) and [LaTeX](https://www.latex-project.org) (optional, if you want to use LaTeX).
For Linux, [Pango](https://pango.org) along with its development headers are required. See instruction [here](https://github.com/ManimCommunity/ManimPango#building).


### Directly

```sh
# Install the fork (pip install manimgl would install upstream instead)
pip install git+https://github.com/zavden/manimgl_3b1b.git

# Try it out
manimgl
```

For more options, take a look at the [Using manim](#using-manim) sections further below.

If you want to hack on manimlib itself, clone this repository and in that directory execute:

```sh
# Install manimgl
pip install -e .

# Try it out
manimgl example_scenes.py OpeningManimExample
# or
manim-render example_scenes.py OpeningManimExample
```

### Linux (Ubuntu/Debian)

1. Install system dependencies.

```sh
sudo apt update

sudo apt install ffmpeg
sudo apt install python3-pip
sudo apt install libpango1.0-dev
```

2. Install a lightweight LaTeX distribution (optional, for LaTeX rendering).

```sh
sudo apt install texlive-science texlive-fonts-extra texlive-latex-extra
```

This lightweight setup is significantly smaller than installing `texlive-full`
while still supporting most Manim projects.

3. Clone and install ManimGL.

```sh
git clone https://github.com/zavden/manimgl_3b1b.git
cd manimgl_3b1b

python3 -m pip install -e .

manimgl example_scenes.py OpeningManimExample
```

<details>
  <summary>💡 Optional: Using a virtual environment (venv)</summary>

It is recommended to use a virtual environment to avoid conflicts with system packages.

```sh
sudo apt install python3-venv

python3 -m venv venv
source venv/bin/activate

python3 -m pip install -e .
```

If `python3-venv` is unavailable on your system, try installing the version-specific package instead:

```sh
sudo apt install python3.12-venv
```

</details>

### Directly (Windows)

1. [Install FFmpeg](https://www.wikihow.com/Install-FFmpeg-on-Windows).
2. Install a LaTeX distribution. [MiKTeX](https://miktex.org/download) is recommended.
3. Install the remaining Python packages.
    ```sh
    git clone https://github.com/zavden/manimgl_3b1b.git
    cd manimgl_3b1b
    pip install -e .
    manimgl example_scenes.py OpeningManimExample
    ```

### Mac OSX

1. Install FFmpeg, LaTeX in terminal using homebrew.
    ```sh
    brew install ffmpeg mactex
    ```
    <details>
      <summary>💡 An alternative to heavyweight MacTeX bundle.</summary>

      > To avoid installing the full MacTeX bundle, which is ~6GB, you can alternatively install the
      > lightweight [BasicTeX](https://formulae.brew.sh/cask/basictex) and then gradually add
      > only the LaTeX packages you actually need. A list of packages sufficient to run examples can 
      > be found [here](https://github.com/3b1b/manim/issues/2133#issuecomment-2414547866).
      > For an overview of the MacTeX installer bundles, see https://www.tug.org/mactex/.
    </details>

2. If you are using an ARM-based processor, install Cairo. 
    ```sh
    arch -arm64 brew install pkg-config cairo
    ```
   
3. Install latest version of manim using these command.
    ```sh
    git clone https://github.com/zavden/manimgl_3b1b.git
    cd manimgl_3b1b
    pip install -e .
    manimgl example_scenes.py OpeningManimExample
    ```
    If the last command is not found, make sure the directory pip installed `manimgl` into is on your `PATH`.

## Anaconda Install

1. Install LaTeX as above.
2. Create a conda environment using `conda create -n manim python=3.10`.
3. Activate the environment using `conda activate manim`.
4. Install manimgl using `pip install -e .`.


## Using manim
Try running the following:
```sh
manimgl example_scenes.py OpeningManimExample
```
This should pop up a window playing a simple scene.

Look through the [example scenes](https://3b1b.github.io/manim/getting_started/example_scenes.html) to see examples of the library's syntax, animation types and object types. In the [3b1b/videos](https://github.com/3b1b/videos) repo, you can see all the code for 3blue1brown videos, though code from older videos may not be compatible with the most recent version of manim. The readme of that repo also outlines some details for how to set up a more interactive workflow, as shown in [this manim demo video](https://www.youtube.com/watch?v=rbu7Zu5X1zI) for example.

When running in the CLI, some useful flags include:
* `-w` to write the scene to a file
* `-o` to write the scene to a file and open the result
* `-s` to skip to the end and just show the final frame.
    * `-so` will save the final frame to an image and show it
* `-n <number>` to skip ahead to the `n`'th animation of a scene.
* `-f` to make the playback window fullscreen

Take a look at custom_config.yml for further configuration.  To add your customization, you can either edit this file, or add another file by the same name "custom_config.yml" to whatever directory you are running manim from.  For example [this is the one](https://github.com/3b1b/videos/blob/master/custom_config.yml) for 3blue1brown videos.  There you can specify where videos should be output to, where manim should look for image files and sounds you want to read in, and other defaults regarding style and video quality.

### Documentation
Documentation is in progress at [3b1b.github.io/manim](https://3b1b.github.io/manim/). And there is also a Chinese version maintained by [**@manim-kindergarten**](https://manim.org.cn): [docs.manim.org.cn](https://docs.manim.org.cn/) (in Chinese).

[manim-kindergarten](https://github.com/manim-kindergarten/) wrote and collected some useful extra classes and some codes of videos in [manim_sandbox repo](https://github.com/manim-kindergarten/manim_sandbox).


## Contributing
Is always welcome.  As mentioned above, the [community edition](https://github.com/ManimCommunity/manim) has the most active ecosystem for contributions, with testing and continuous integration, but pull requests are welcome here too.  Please explain the motivation for a given change and examples of its effect.


## License
This project falls under the MIT license.
