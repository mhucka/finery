# Urial<img width="12%" align="right" src="https://github.com/mhucka/urial/raw/main/.graphics/urial-icon.png">

Urial (_**URI** **a**ddition too**l**_) is a simple but intelligent command-line tool to add or replace URIs found inside macOS Finder comments.

[![License](https://img.shields.io/badge/License-BSD%203--Clause-blue.svg?style=flat-square)](https://choosealicense.com/licenses/bsd-3-clause)
[![Latest release](https://img.shields.io/github/v/release/mhucka/urial.svg?style=flat-square&color=b44e88)](https://github.com/mhucka/urial/releases)


## Table of contents

* [Introduction](#introduction)
* [Installation](#installation)
* [Usage](#usage)
* [Getting help](#getting-help)
* [Contributing](#contributing)
* [License](#license)
* [Authors and history](#authors-and-history)
* [Acknowledgments](#authors-and-acknowledgments)


## Introduction

Urial is a command-line program written in Python 3 that allows you to write and update URIs in the Finder comments of a file. Urial makes it easier to write scripts (e.g., in Bash/Bourne shell syntax, or AppleScripts) that keep those URIs updated.

Incidentally, [urial](https://en.wikipedia.org/wiki/Urial) (properly known as _Ovis vignei_) are a kind of wild sheep native to Central and South Asia. They are listed as a [vulnerable species](https://www.iucnredlist.org/species/54940655/195296049) and their population continues to twindle due to human activity, hunting, and climate change.


## Installation

There are multiple ways of installing Urial, ranging from downloading a self-contained, single-file, ready-to-run program, to installing it as a typical Python program using `pip`.  Please choose the alternative that suits you.

### _Alternative 1: installing Urial using `pipx`_

You can use [pipx](https://pypa.github.io/pipx/) to install Urial. Pipx will install it into a separate Python environment that isolates the dependencies needed by Urial from other Python programs on your system, and yet the resulting `urial` command wil be executable from any shell &ndash; like any normal program on your computer. If you do not already have `pipx` on your system, it can be installed in a variety of easy ways and it is best to consult [Pipx's installation guide](https://pypa.github.io/pipx/installation/) for instructions. Once you have pipx on your system, you can install Urial with the following command:
```sh
pipx install urial
```

Pipx can also let you run Urial directly using `pipx run urial`, although in that case, you must always prefix every Urial command with `pipx run`.  Consult the [documentation for `pipx run`](https://github.com/pypa/pipx#walkthrough-running-an-application-in-a-temporary-virtual-environment) for more information.


### _Alternative 2: installing Urial using `pip`_

The instructions below assume you have a Python 3 interpreter installed on your computer.  Note that the default on macOS at least through 10.14 (Mojave) is Python **2** &ndash; please first install Python version 3 and familiarize yourself with running Python programs on your system before proceeding further.

You should be able to install `urial` with [`pip`](https://pip.pypa.io/en/stable/installing/) for Python&nbsp;3.  To install `urial` from the [Python package repository (PyPI)](https://pypi.org), run the following command:
```sh
python3 -m pip install urial
```

As an alternative to getting it from [PyPI](https://pypi.org), you can use `pip` to install `urial` directly from GitHub:
```sh
python3 -m pip install git+https://github.com/mhucka/urial.git
```

_If you already installed Urial once before_, and want to update to the latest version, add `--upgrade` to the end of either command line above.


### _Alternative 3: installing Urial from sources_

If  you prefer to install Urial directly from the source code, you can do that too. To get a copy of the files, you can clone the GitHub repository:
```sh
git clone https://github.com/mhucka/urial
```

Alternatively, you can download the files as a ZIP archive using this link directly from your browser using this link: <https://github.com/mhucka/urial/archive/refs/heads/main.zip>

Next, after getting a copy of the files,  run `setup.py` inside the code directory:
```sh
cd urial
python3 setup.py install
```


## Usage

This program expects to be given at least two argument values.  The first value is taken to be a string containing a URI, and the second value is the path of a file whose Finder comment should be updated with the given string.  Optional arguments begin with dashes and modify the program's behavior.

By default, if file already has any Finder comment, it is only modified to update the substring that has the same type of URI, and then only if the Finder comment contains such a substring.  For example, if the file "somefile.md" contains a Finder comment with an existing `x-devonthink-item` URI inside of it, then the following command,

```sh
urial "x-devonthink-item://8A1A0F18-068680226F3" somefile.md
```

will rewrite the URI part of the comment to have the new URI given on the command line.  If the Finder comment is not empty but does not contain a URI of the same kind as the one given on the command line, then the Finder comment is not changed unless a suitable value for the option `--mode` is given (see below). 

Urial users regular expression pattern matching to find the same kind of URI as the value you provide on the command line, to make it more robust against accidentally matching other URIs that may exist in a Finder comment. So, for example, If you supply a URI that has a `x-devonthink-item` scheme type, it will look only for `x-devonthink-item` URIs and will not match other URIs; if you supply a URI that has a `zotero` scheme type, it will look only for those URIs; and so on.


### Handling existing Finder comments

If the file already has a Finder comment, the default behavior of `urial` is to first check if the comment contains a URI of the same scheme as the given URI; if it does, `urial` replaces the URI (and just the URI) substring in the Finder comment, and if it does not, `urial` instead _appends_ the URL to the comment.  The `--mode` option can be used to change this behavior, as follows:

* `append`: if the URI is NOT found in the Finder comment string, append the given URI to the end of the comment; otherwise (if the comment string already contains the URI) do nothing
* `overwrite`: overwrite the Finder comment completely with the given URI string, no matter what the Finder comment string contains (even if it already contains the given URI)
  `update`: (default) if a URI of the same kind exists in the comment, replace only the URI portion of the comment string (preserving the rest of the comment string), else (if a URI is NOT found in the comment string) do nothing

Note that the behavior of `--mode overwrite` is to replace unconditionally the entire Finder comment.  In other words, `-- mode overwrite` will change a Finder comment such as

    Blah blah blah. URI. More blah blah blah.

to just

    URI

assuming that `URI` is the URI given to urial on the command line.  If you want to update the URI to a new value and leave the other comment text in place, use `--mode update` or simply don't provide a value for `--mode` (because `update` is the default action).


### Additional command-line arguments

If given the `--version` option, this program will print the version and other information, and exit without doing anything else.

By default, this program will use macOS dialogs to report errors or other issues.  The option `--no-gui` will make it print messages only on the command line, without using GUI dialogs.

If given the `--debug` argument, this program will output a detailed trace of what it is doing. The trace will be sent to the given destination, which can be `-` to indicate console output, or a file path to send the output to a file.


## Getting help

If you find an issue, please submit it in [the GitHub issue tracker](https://github.com/mhucka/urial/issues) for this repository.


## Contributing

I would be happy to receive your help and participation if you are interested.  Everyone is asked to read and respect the [code of conduct](CONDUCT.md) when participating in this project.  Development generally takes place on the `development` branch.


## License

This software is Copyright (C) 2021, by Michael Hucka and the California Institute of Technology (Pasadena, California, USA).  This software is freely distributed under a 3-clause BSD type license.  Please see the [LICENSE](LICENSE) file for more information.


## Acknowledgments

This work is a personal project developed by the author, using computing facilities and other resources of the [California Institute of Technology Library](https://www.library.caltech.edu).

The [vector artwork](https://thenounproject.com/icon/bighorn-sheep-head-2608122/) of a sheep with horns, used as the icon for Urial, was created by  [Vectors Point](https://thenounproject.com/vectorspoint/) from the Noun Project.  It is licensed under the Creative Commons [CC-BY 3.0](https://creativecommons.org/licenses/by/3.0/) license.

Urial makes use of numerous open-source packages, without which Urial could not have been developed.  I want to acknowledge this debt.  In alphabetical order, the packages are:

* [appscript](http://appscript.sourceforge.net/py-appscript/doc.html) &ndash; high-level Apple event bridge that allows you to control scriptable Mac OS X applications
* [plac](http://micheles.github.io/plac/) &ndash; a command line argument parser
* [setuptools](https://github.com/pypa/setuptools) &ndash; library for `setup.py`
* [Sidetrack](https://github.com/caltechlibrary/sidetrack) &ndash; simple debug logging/tracing package
* [wheel](https://pypi.org/project/wheel/) &ndash; setuptools extension for building wheels
