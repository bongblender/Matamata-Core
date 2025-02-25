# Matamata Core

Matamata (an acronym for "Matamata attempts to animate mouths, at times accurately") is a tool to automatically create lip-synced animations.

<img src="https://raw.githubusercontent.com/Matamata-Animator/Branding/main/Logos-Icons/logo.png" alt="logo" width="200" height="200"/>

## Table of Contents

- [Table of Contents](#table-of-contents)
- [Installation](#Installation)
  - [Read This First!!!](#Pick-Your-Poison)
  - [Windows](#Windows)
  - [Ubuntu](#Ubuntu)
  - [Mac](#Mac)
- [Setup](#setup)
  - [Character File](#character-file)
  - [Timestamps](#timestamps)
  - [Mouths](#mouths)
- [Usage](#usage)
  - [Flags and Arguments](#flags-and-arguments)
  - [Custom Defaults](#Custom-Defaults)
  - [Running](#Running)
- [Contributing](#Contributing)

## Installation

### Pick Your Poison

Matamata currently supports two methods of phoneme alignment, Allosaurus and Gentle. Allosaurus is easier to setup and it performs better in loud environments, however its alignment is often not as accurate. Gentle alignment requires Docker Desktop, which can be harder to install but does not require python and generally provides better alignment. Please install one (or both) of these options, **Gentle is currently the recommended option.** Keep in mind you can use either of these options by specifying `--aligning_algorithm allosaurus | gentle` when running the program.

Currently Allosaurus is in a development state and is not necessarily usable for large projects.

**Gentle**

This will not work on Macs with apple silicon.

Install [Docker Desktop](https://www.docker.com/products/docker-desktop) for your operating system

Run `docker pull lower quality/gentle` in your command prompt/terminal

**Allosaurus**

All allosaurus requires is python.

On Windows:
Download and install [python3.9](https://www.python.org/downloads/release/python-399/), make sure to select the option to add python3 to path during install. You can test to see if this worked by running `python` in your terminal.

On Mac:
Install using [Homebrew]('https://brew.sh')
`brew install python3`

On Ubuntu:
`sudo apt install python3 python-is-python3`


Also install the required pip packages:
`pip3 install allosaurus`
as well as [pytorch](https://pytorch.org/get-started/locally/)

### Windows
- Install [NodeJS](https://nodejs.org/en/) 16+

  - Make sure to include the optional add-ons

- Install yarn and typescript

```bash
npm install --global yarn typescript
```

- Download the code using git or the button in the top right

```bash
git clone https://github.com/Matamata-Animator/Matamata-Core.git
```

- Open the folder In command prompt and install the dependencies

```
yarn
```

- Install Vosk model through the [Vosk website](https://alphacephei.com/vosk/models) or using the automatic tool. **This is a 1.6 GB file and thus will take some time, please have patience.**

```zsh
yarn downloadModel
```

### Ubuntu

- Clone the repo

```shell
git clone https://github.com/AI-Spawn/Auto-Lip-Sync
cd Auto-Lip-Sync
```

- Install required packages

```shell
sudo apt install docker.io nodejs
```

- Install yarn and typescript

```bash
sudo npm install --global yarn typescript
```

- Open the folder in the terminal and install the dependencies

```
yarn
```

- Install Vosk model through the [Vosk website](https://alphacephei.com/vosk/models) or using the automatic tool. **This is a 1.6 GB file and thus will take some time, please have patience.**

```zsh
yarn downloadModel
```

### Mac

- Install the Node Version Manager (nvm)

```zsh
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
```

- Install Node 6+

```
nvm install 16
```

- Install Yarn and TypeScript

```zsh
npm install -g yarn typescript
```

- Download the code using git or the button in the top right

```zsh
git clone https://github.com/Matamata-Animator/Matamata-Core.git
```

- Open the folder in the terminal and install the dependencies

```zsh
yarn
```

- Install Vosk model through the [Vosk website](https://alphacephei.com/vosk/models) or using the automatic tool. **This is a 1.6 GB file and thus will take some time, please have patience. There is currently no progress bar implemented.**

```zsh
yarn downloadModel
```

## Setup

### Character File

Below is a barebones character file:

```json
{
  "mouthsPath": "defaults/mouths/",
  "poses": {
    "imagesFolder": "defaults/SampleCharacter/faces/",
    "default": {
      "image": "purple.png",
      "x": 640,
      "y": 400
    },
    "green": {
      "image": "blue.png",
      "x": 640,
      "y": 400
    }
  }
}
```

`mouthsPath` specifies the path to a folder containing the mouth images.

`poses` contains two main elements. `imagesFolder` specifies the path to the folder which contains the pose images. `default` is a pose object. `image` refers to the name of the image inside the imagesFolder. `x` and `y` are the coordinates where the mouth should be placed. More poses can be created with more pose objects, as is shown with the `green` pose.

A more fleshed out character file could look like this:

```json
{
  "mouthsPath": "defaults/mouths/",
  "poses": {
    "imagesFolder": "defaults/SampleCharacter/faces/",
    "default_scale": 2,
    "default": {
      "image": "purple.png",
      "x": 640,
      "y": 400,
      "facingLeft": false,
      "scale": 2
    },
    "green": {
      "image": "green.png",
      "x": 640,
      "y": 400,
      "facingLeft": false,
      "scale": 1
    }
  },
  "eyes": {
    "imagesFolder": "defaults/SampleCharacter/eyes/",
    "scale": 0.8,
    "x": 640,
    "y": 300,
    "images": {
      "angry": "angry.png",
      "normal": "normal.png",
      "sad": "sad.png"
    }
  }
}
```

`default_scale` says how much the mouth should be scaled up or down. `scale` is the same thing for a specific pose. In this case, the mouths for the `default` pose with be 4x the image size, while the mouths for the `green` pose will only be 2x the size.

`eyes` specifies a "placeable part". The sample character pose images don't have eyes, as these are specified by placeable parts. Although this example has placeable eyes, you can have placeable pins, objects in the background, or even hats. The `imagesFolder` specifies the path to the folder contains the images for the placeable part. `scale` specifies how much the placeable part image should be scaled up or down. `x` and `y` specify the location on the pose where the part should be placed. `images` contained key-value pairs where the key is the name of the part, and the value is the image name. This section shows angry, normal, and sad eye selections.

### Timestamps

The timestamps file is composed of a list of pose changes along with how many milliseconds into the animation the pose should change. For instance, if you wanted to swap to the `happy` pose after 3.5 seconds, the timestamps file will look like:

> 3500 happy

Additionally, you can change a placeable part by adding the type afterwards.

> 0 angry eyes

You can also remove a placeable part by using the name `None`

> 5000 None eyes

## Usage

### Arguments

#### Default Arguments

This covers the most important flags and arguments. For the complete list, go to [Default Arguments](defaults/default_args.json).

| Shortcut | Command              | Required | Default                    | Type                 | Description                                      |
| -------- | -------------------- | -------- | -------------------------- | -------------------- | ------------------------------------------------ |
| --a      | --audio              | \*       |                            | str                  | The path to the audio file being animated        |
|          | --aligning_algorithm |          | gentle                     | gentle \| allosaurus | The aligning algorithm to be used.               |
| --t      | --timestamps         |          |                            | str                  | The path to the file containing pose timestamps. |
| --o      | --output             |          | "defaults/output.mp4"      | str                  | The output of the program                        |
| --c      | --character          |          | "defaults/characters.json" | str                  | The list of character poses                      |
| --m      | --mouths             |          | "defaults/phonemes.json"   | str                  | The mouth pack and phonemes list                 |
| --V      | --verbose            |          | 1                          | int                  | Dump process outputs to the shell                |

#### Custom Defaults

You can set custom default arguments by creating a file `config.json` in the main folder. In this file, the key is the command and the value is what you want the new default to be. For instance, if you wanted to always be set to verbose mode 3, your file will be:

```json
{
  "verbose": 3
}
```

### Running

The command to create an animation is the same for all supported platforms

```shell
yarn animate --a audio.wav [optional arguments]
```

## Contributing

Do you use this project and want to see a new feature added? Open an issue with the tag _feature request_ and say what you want.

Want to try your hand at writing code? Create a fork, upload your code, and make a pull request. Anything from fixing formatting/typos to entirely new features is welcome!

Don't know what to work on? Take a look at the issues page to see what improvements people want. Anything marked _good first issue_ should be great for newcomers!
