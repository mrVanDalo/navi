# navi <img src="https://user-images.githubusercontent.com/3226564/65362934-b4432500-dbdf-11e9-8f75-815fbc5cbf8f.png" alt="icon" height="28px"/> [![Actions Status](https://github.com/denisidoro/navi/workflows/Tests/badge.svg)](https://github.com/denisidoro/navi/actions) [![Build Status](https://travis-ci.org/denisidoro/navi.svg?branch=master)](https://travis-ci.org/denisidoro/navi) ![GitHub release](https://img.shields.io/github/v/release/denisidoro/navi?include_prereleases)
  
> :information_source: If you're here because you upgraded **navi** and are having some issues, please check [this thread](https://github.com/denisidoro/navi/issues/201).

An interactive cheatsheet tool for the command-line.

![Demo](https://user-images.githubusercontent.com/3226564/76437136-ddc35900-6397-11ea-823c-d2da7615fe60.gif)

**navi** allows you to browse through cheatsheets (that you may write yourself or download from maintainers) and execute commands. Argument suggestions are prompted to you.

#### Pros
- it will make you type less;
- it will spare you from knowing CLIs by heart;
- it will teach you new one-liners.

It uses [fzf](https://github.com/junegunn/fzf) under the hood and it can be either used as a command or as a shell widget (*à la* Ctrl-R).

afdasfsadf

Table of contents
-----------------

   * [Installation](#installation)
      * [Using Homebrew or Linuxbrew](#using-homebrew-or-linuxbrew)
      * [Using cargo](#using-cargo)
      * [Using install script](#using-install-script)
      * [Downloading pre-compiled binaries](#downloading-pre-compiled-binaries)
      * [Building from source](#building-from-source)
   * [Usage](#usage)
      * [Shell widget](#shell-widget)
      * [More options](#more-options)
   * [Trying out online](#trying-out-online)
   * [Cheatsheets](#cheatsheets)
      * [Importing cheatsheets](#importing-cheatsheets)
      * [Adding your own cheatsheets](#adding-your-own-cheatsheets)
      * [Submitting cheatsheets](#submitting-cheatsheets)
   * [Cheatsheet syntax](#cheatsheet-syntax)
      * [Syntax overview](#syntax-overview)
      * [Variables](#variables)
      * [Advanced variable options](#advanced-variable-options)
      * [Variable dependency](#variable-dependency)
      * [Multiline snippets](#multiline-snippets)
   * [List customization](#list-customization)
   * [Related projects](#related-projects)
   * [Etymology](#etymology)

Installation
------------

### Using [Homebrew](http://brew.sh/) or [Linuxbrew](http://linuxbrew.sh/)

```sh
brew install denisidoro/tools/navi
```

Alternatively, you can use the official formula (but it will install a very old version):
```sh
brew install navi
```

### Using [cargo](https://github.com/rust-lang/cargo)

```bash
cargo install navi
```

### Using install script

```bash
bash <(curl -sL https://raw.githubusercontent.com/denisidoro/navi/master/scripts/install)

# alternatively, to set directories:
# SOURCE_DIR=/opt/navi BIN_DIR=/usr/local/bin bash <(curl -sL https://raw.githubusercontent.com/denisidoro/navi/master/scripts/install)l
```

### Downloading pre-compiled binaries

You can download binaries [here](https://github.com/denisidoro/navi/releases/latest).

### Building from source

```bash
git clone https://github.com/denisidoro/navi ~/.navi
cd ~/.navi
make install 

# alternatively, to set install directory:
# make BIN_DIR=/usr/local/bin install
```


Usage
-----

By running `navi` for the first time, you'll be suggested to download some cheatsheets. By running `navi` again, these cheatsheets will appear.

### Shell widget

You can use **navi** as a widget to your shell. This way, your history is correctly populated and you can edit the command as you wish before executing it. To set it up, add this line to your `.bashrc`-like file:
```sh
# bash
source <(navi widget bash)

# zsh
source <(navi widget zsh)

# fish
navi widget fish | source
```

By default, `Ctrl+G` is assigned to launching **navi**.

### More options

Please refer to `navi --help` for more details.


Trying out online
--------------------

If you don't have access to a Unix shell at the moment and you want to live preview **navi**, head to [this playground](https://www.katacoda.com/denisidoro/scenarios/navi). It'll start a docker container with instructions for you to install and use the tool. Note: login required.


Cheatsheets
-----------

### Importing cheatsheets

**navi** is able to import cheatsheets from git repositories:
```
navi repo add https://github.com/denisidoro/navi
```

### Adding your own cheatsheets

You can either start a git repo with cheatsheets and import it as described above or you can add them directly to [data_dir](https://github.com/soc/dirs-rs#Features)`/navi`.


### Submitting cheatsheets

The main repository for cheatsheets is [denisidoro/cheats](https://github.com/denisidoro/cheats). Feel free to open a PR there for me to include your contributions.


Cheatsheet syntax
-----------------

Cheatsheets are described in `.cheat` files that look like this:

```sh
% git, code

# Change branch
git checkout <branch>

$ branch: git branch | awk '{print $NF}'
```

### Syntax overview

- lines starting with `%` determine the start of a new cheatsheet and should contain tags, useful for searching;
- lines starting with `#` should be descriptions of commands;
- lines starting with `;` are ignored. You can use them for metacomments;
- lines starting with `$` should contain commands that generate a list of possible values for a given argument;
- all the other non-empty lines are considered as executable commands.

It's irrelevant how many files are used to store cheatsheets. They can be all in a single file if you wish, as long as you split them accordingly with lines starting with `%`.

### Variables

The interface prompts for variable names inside brackets (eg `<branch>`). 

Variable names should only include alphanumeric characters and `_`.

If there's a corresponding line starting with `$` for a variable, suggestions will be displayed. Otherwise, the user will be able to type any value for it.

### Advanced variable options

For lines starting with `$` you can add use `---` to customize the behavior of `fzf` or how the value is going to be used:

```sh
# This will pick the 3rd column and use the first line as header
docker rmi <image_id>

$ image_id: docker images --- --column 3 --header-lines 1 --delimiter '\s\s+'
```

The supported parameters are:
- `--allow-extra` *(experimental)*: handles `fzf` option `--print-query`. `enter` will prefer a selection and `tab` will prefer the query typed. 
- `--multi` : forwarded option to `fzf`.
- `--header-lines` : forwarded option to `fzf`
- `--column` : forwarded option to `fzf`.
- `--delimiter` : forwarded option to `fzf`.

### Variable dependency

The command for generating possible inputs can refer previous variables:
```sh
# If you select 2 for x, the possible values of y will be 12 and 22
echo <x> <y>

$ x: echo '1 2 3' | tr ' ' '\n'
$ y: echo "$((x+10)) $((x+20))" | tr ' ' '\n'
```

### Multiline snippets

Commands may be multiline:
```sh
# This will output "foo\nyes"
echo foo
true \
   && echo yes \
   || echo no
```


List customization
------------------

Lists can be stylized with the [$FZF_DEFAULT_OPTS](https://github.com/junegunn/fzf#layout) environment variable or similar variables or parameters ( please refer to `navi --help`). This way, you can change the [color scheme](https://github.com/junegunn/fzf/wiki/Color-schemes), for example.


Related projects
----------------

There are many similar projects out there ([bro](https://github.com/hubsmoke/bro), [eg](https://github.com/srsudar/eg), [cheat.sh](https://github.com/chubin/cheat.sh), [tldr](https://github.com/tldr-pages/tldr), [cmdmenu](https://github.com/amacfie/cmdmenu), [cheat](https://github.com/cheat/cheat), [beavr](https://github.com/denisidoro/beavr), [how2](https://github.com/santinic/how2) and [howdoi](https://github.com/gleitz/howdoi), to name a few).

Most of them provide excellent cheatsheet repositories, but lack a nice UI and argument suggestions.


Etymology
---------

In [The Legend of Zelda Ocarina of Time](https://zelda.gamepedia.com/Ocarina_of_Time), [navi](https://zelda.gamepedia.com/Navi) is a character that provides [Link](https://zelda.gamepedia.com/Link) with a variety of clues to help him solve puzzles and progress in his quest.
