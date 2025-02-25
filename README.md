# bump2version

[![image](https://img.shields.io/pypi/v/bump2version.svg)](https://pypi.org/project/bump2version/)
[![image](https://img.shields.io/pypi/l/bump2version.svg)](https://pypi.org/project/bump2version/)
[![image](https://img.shields.io/pypi/pyversions/bump2version.svg)](https://pypi.org/project/bump2version/)
[![GitHub Actions](https://github.com/c4urself/bump2version/workflows/CI/badge.svg)](https://github.com/c4urself/bump2version/actions)

## NOTE

This is a maintained fork of the excellent [bumpversion project](https://github.com/peritus/bumpversion).

## Overview

Version-bump your software with a single command!

A small command line tool to simplify releasing software by updating all
version strings in your source code by the correct increment. Also creates
commits and tags:

* version formats are highly configurable
* works without any VCS, but happily reads tag information from and writes
  commits and tags to Git and Mercurial if available
* just handles text files, so it's not specific to any programming language
* supports Python 3 and PyPy3

If you want to use Python 2, use `pip>=9` and you'll get the last supported version,
or pin `bump2version<1`.

## Alternatives

If bump2version does not fully suit your needs, you could take a look
at other tools doing similar or related tasks:
[ALTERNATIVES.md](https://github.com/c4urself/bump2version/blob/master/RELATED.md).

## Installation

You can download and install the latest version of this software from the Python package index (PyPI) as follows:

    pip install --upgrade bump2version

**NOTE: `pip install bumpversion` now installs the latest bump2version!**

## Changelog

Please find the changelog here: [CHANGELOG.md](CHANGELOG.md)

## Usage

NOTE: Throughout this document you can use `bumpversion` or `bump2version` interchangeably.

There are two modes of operation: On the command line for single-file operation
and using a configuration file (`.bumpversion.cfg`) for more complex multi-file operations.

    bump2version [options] part [file]

#### `part`
  _**required**_<br />

  The part of the version to increase, e.g. `minor`.

  Valid values include those given in the `--serialize` / `--parse` option.

  Example bumping 0.5.1 to 0.6.0:

     bump2version --current-version 0.5.1 minor src/VERSION

#### `file`
  _**[optional]**_<br />
  **default**: none

  The file that will be modified.

  This file is added to the list of files specified in `[bumpversion:file:…]`
  sections from the configuration file. If you want to rewrite only files
  specified on the command line, use `--no-configured-files`.

  Example bumping 1.1.9 to 2.0.0:

     bump2version --current-version 1.1.9 major setup.py

## Configuration file

All options can optionally be specified in a config file called
`.bumpversion.cfg` so that once you know how `bump2version` needs to be
configured for one particular software package, you can run it without
specifying options later. You should add that file to VCS so others can also
bump versions.

Options on the command line take precedence over those from the config file,
which take precedence over those derived from the environment and then from the
defaults.

Example `.bumpversion.cfg`:

```ini
[bumpversion]
current_version = 0.2.9
commit = True
tag = True

[bumpversion:file:setup.py]
```

If no `.bumpversion.cfg` exists, `bump2version` will also look into
`setup.cfg` for configuration.

### Configuration file -- Global configuration

General configuration is grouped in a `[bumpversion]` section.

#### `current_version`
  _**required**_<br />
  **default**: none

  The current version of the software package before bumping.

  Also available as `--current-version` (e.g. `bump2version --current-version 0.5.1 patch setup.py`)

#### `new_version`
  _**[optional]**_<br />
  **default**: none

  The version of the software package after the increment. If not given will be
  automatically determined.

  Also available as `--new-version` (e.g. `to go from 0.5.1 directly to
  0.6.1`: `bump2version --current-version 0.5.1 --new-version 0.6.1 patch
  setup.py`).

#### `tag = (True | False)`
  _**[optional]**_<br />
  **default**: False (Don't create a tag)

  Whether to create a tag, that is the new version, prefixed with the character
  "`v`". If you are using git, don't forget to `git-push` with the
  `--tags` flag.

  Also available on the command line as `(--tag | --no-tag)`.

#### `sign_tags = (True | False)`
  _**[optional]**_<br />
  **default**: False (Don't sign tags)

  Whether to sign tags.

  Also available on the command line as `(--sign-tags | --no-sign-tags)`.

#### `tag_name =`
  _**[optional]**_<br />
  **default:** `v{new_version}`

  The name of the tag that will be created. Only valid when using `--tag` / `tag = True`.

  This is templated using the [Python Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax).
  Available in the template context are `current_version` and `new_version`
  as well as `current_[part]` and `new_[part]` (e.g. '`current_major`'
  or '`new_patch`').
  In addition, all environment variables are exposed, prefixed with `$`.
  You can also use the variables `now` or `utcnow` to get a current timestamp. Both accept
  datetime formatting (when used like as in `{now:%d.%m.%Y}`).

  Also available as command-line flag `tag-name`.  Example usage:
  `bump2version --tag-name 'release-{new_version}' patch`

#### `tag_message =`
  _**[optional]**_<br />
  **default:** `Bump version: {current_version} → {new_version}`

  The tag message to use when creating a tag. Only valid when using `--tag` / `tag = True`.

  This is templated using the [Python Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax).
  Available in the template context are `current_version` and `new_version`
  as well as `current_[part]` and `new_[part]` (e.g. '`current_major`'
  or '`new_patch`').
  In addition, all environment variables are exposed, prefixed with `$`.
  You can also use the variables `now` or `utcnow` to get a current timestamp. Both accept
  datetime formatting (when used like as in `{now:%d.%m.%Y}`).

  Also available as command-line flag `--tag-message`.  Example usage:
  `bump2version --tag-message 'Release {new_version}' patch`

  `bump2version` creates an `annotated` tag in Git by default. To disable this and create a `lightweight` tag, you must explicitly set an empty `tag_message`:

  * either in the configuration file: `tag_message =`
  * or in the command-line: `bump2version --tag-message ''`

  You can read more about Git tagging [here](https://git-scm.com/book/en/v2/Git-Basics-Tagging).

#### `commit = (True | False)`
  _**[optional]**_<br />
  **default:** False (Don't create a commit)

  Whether to create a commit using git or Mercurial.

  Also available as `(--commit | --no-commit)`.

  In many projects it is common to have a pre-commit hook that runs prior to a
  commit and in case of failure aborts the commit. For some use cases it might
  be desired that when bumping a version and having `commit = True`, the
  pre-commit hook should perform slightly different actions than in regular
  commits. For example run an extended set of checks only for actual releases of
  the software. To allow the pre-commit hooks to distinguish a bumpversion
  commit, the `BUMPVERSION_CURRENT_VERSION` and `BUMPVERSION_NEW_VERSION`
  environment variables are set when executing the commit command.

#### `message =`
  _**[optional]**_<br />
  **default:** `Bump version: {current_version} → {new_version}`

  The commit message to use when creating a commit. Only valid when using `--commit` / `commit = True`.

  This is templated using the [Python Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax).
  Available in the template context are `current_version` and `new_version`
  as well as `current_[part]` and `new_[part]` (e.g. '`current_major`'
  or '`new_patch`').
  In addition, all environment variables are exposed, prefixed with `$`.
  You can also use the variables `now` or `utcnow` to get a current timestamp. Both accept
  datetime formatting (when used like as in `{now:%d.%m.%Y}`).

  Also available as command-line flag `--message`.  Example usage:
  `bump2version --message '[{now:%Y-%m-%d}] Jenkins Build {$BUILD_NUMBER}: {new_version}' patch`)

#### `commit_args =`
  _**[optional]**_<br />
  **default:** empty

  Extra arguments to pass to commit command. Only valid when using `--commit` /
  `commit = True`.

  This is for example useful to add `-s` to generate `Signed-off-by:` line in
  the commit message.

  Multiple arguments can be specified on separate lines.

  Also available as command-line flag `--commit-args`, in which case only one
  argument can be specified.


### Configuration file -- Part specific configuration

A version string consists of one or more parts, e.g. the version `1.0.2`
has three parts, separated by a dot (`.`) character. In the default
configuration these parts are named `major`, `minor`, `patch`, however you can
customize that using the `parse`/`serialize` option.

By default all parts are considered numeric, that is their initial value is `0`
and they are increased as integers. Also, the value `0` is considered to be
optional if it's not needed for serialization, i.e. the version `1.4.0` is
equal to `1.4` if `{major}.{minor}` is given as a `serialize` value.

For advanced versioning schemes, non-numeric parts may be desirable (e.g. to
identify [alpha or beta versions](http://en.wikipedia.org/wiki/Software_release_life_cycle#Stages_of_development)
to indicate the stage of development, the flavor of the software package or
a release name). To do so, you can use a `[bumpversion:part:…]` section
containing the part's name (e.g. a part named `release_name` is configured in
a section called `[bumpversion:part:release_name]`.

The following options are valid inside a part configuration:

#### `values =`
  **default**: numeric (i.e. `0`, `1`, `2`, …)

  Explicit list of all values that will be iterated when bumping that specific
  part.

  Example:

```ini
[bumpversion:part:release_name]
values =
  witty-warthog
  ridiculous-rat
  marvelous-mantis
```

#### `optional_value =`
  **default**: The first entry in `values =`.

  If the value of the part matches this value it is considered optional, i.e.
  its representation in a `--serialize` possibility is not required.

  Example:

```ini
[bumpversion]
current_version = 1.alpha
parse = (?P<num>\d+)(\.(?P<release>.*))?
serialize =
  {num}.{release}
  {num}

[bumpversion:part:release]
optional_value = gamma
values =
  alpha
  beta
  gamma
```

  Here, `bump2version release` would bump `1.alpha` to `1.beta`. Executing
  `bump2version release` again would bump `1.beta` to `1`, because
  `release` being `gamma` is configured optional.

  You should consider the version of `1` to technically be `1.gamma`
  with the `.gamma` part not being serialized since it is optional.
  The `{num}` entry in the `serialize` list allows the release part to be
  hidden. If you only had `{num}.{release}`, an optional release will always
  be serialized.

  Attempting to bump the release when it is the value of
  `gamma` will cause a `ValueError` as it will think you are trying to
  exceed the `values` list of the release part.

#### `first_value =`
  **default**: The first entry in `values =`.

  When the part is reset, the value will be set to the value specified here.

  Example:

```ini
[bumpversion]
current_version = 1.alpha1
parse = (?P<num>\d+)(\.(?P<release>.*)(?P<build>\d+))?
serialize =
  {num}.{release}{build}

[bumpversion:part:release]
values =
  alpha
  beta
  gamma

[bumpversion:part:build]
first_value = 1
```

  Here, `bump2version release` would bump `1.alpha1` to `1.beta1`.

  Without the `first_value = 1` of the build part configured,
  `bump2version release` would bump `1.alpha1` to `1.beta0`, starting
  the build at `0`.


#### `independent =`
  **default**: `False`

  When this value is set to `True`, the part is not reset when other parts are incremented. Its incrementation is
  independent of the other parts. It is in particular useful when you have a build number in your version that is
  incremented independently of the actual version.

  Example:

```ini
[bumpversion]
current_version: 2.1.6-5123
parse = (?P<major>\d+)\.(?P<minor>\d+)\.(?P<patch>\d+)\-(?P<build>\d+)
serialize = {major}.{minor}.{patch}-{build}

[bumpversion:file:VERSION.txt]

[bumpversion:part:build]
independent = True
```

  Here, `bump2version build` would bump `2.1.6-5123` to `2.1.6-5124`. Executing`bump2version major`
  would bump `2.1.6-5124` to `3.0.0-5124` without resetting the build number.
  

### Configuration file -- File specific configuration

This configuration is in the section: `[bumpversion:file:…]` or `[bumpversion:glob:…]`

Both, `file:` and `glob:` are configured the same. Their difference is that
file will match file names directly like `requirements.txt`. While glob also
matches multiple files via wildcards like `**/pom.xml`.

Note: The configuration file format requires each section header to be
unique. If you want to process a certain file multiple times,
you may append a description between parens to the `file` keyword:
`[bumpversion:file (special one):…]`.

#### `parse =`
  **default:** `(?P<major>\d+)\.(?P<minor>\d+)\.(?P<patch>\d+)`

  Regular expression (using [Python regular expression syntax](https://docs.python.org/3/library/re.html#regular-expression-syntax)) on
  how to find and parse the version string.

  Is required to parse all strings produced by `serialize =`. Named matching
  groups ("`(?P<name>...)`") provide values to as the `part` argument.

  Also available as `--parse`

#### `serialize =`
  **default:** `{major}.{minor}.{patch}`

  Template specifying how to serialize the version parts back to a version
  string.

  This is templated using the [Python Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax).
  Available in the template context are parsed values of the named groups
  specified in `parse =` as well as all environment variables (prefixed with
  `$`).

  Can be specified multiple times, bumpversion will try the serialization
  formats beginning with the first and choose the last one where all values can
  be represented like this:

```ini
serialize =
  {major}.{minor}
  {major}
```

  Given the example above, the new version `1.9` will be serialized as
  `1.9`, but the version `2.0` will be serialized as `2`.

  Also available as `--serialize`. Multiple values on the command line are
  given like `--serialize {major}.{minor} --serialize {major}`

#### `search =`
  **default:** `{current_version}`

  Template string how to search for the string to be replaced in the file.
  Useful if the remotest possibility exists that the current version number
  might be present multiple times in the file and you mean to only bump one of the
  occurrences. Can be multiple lines, templated using [Python Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax)

#### `replace =`
  **default:** `{new_version}`

  Template to create the string that will replace the current version number in
  the file.

  Given this `requirements.txt`:

    Django>=1.5.6,<1.6
    MyProject==1.5.6

  using this `.bumpversion.cfg` will ensure only the line containing
  `MyProject` will be changed:

```ini
[bumpversion]
current_version = 1.5.6

[bumpversion:file:requirements.txt]
search = MyProject=={current_version}
replace = MyProject=={new_version}
```

  Can be multiple lines, templated using [Python Format String Syntax](https://docs.python.org/3/library/string.html#format-string-syntax).

  **NOTE**: (*Updated in v1.0.1*) It is important to point out that if a
  custom search pattern is configured, then `bump2version` will only perform
  a change if it finds an exact match and will not fallback to the default
  pattern. This is to prevent accidentally changing strings that match the
  default pattern when there is a typo in the custom search pattern.

  For example, if the string to be replaced includes literal quotes,
  the search and replace patterns must include them too to match. Given the
  file `version.sh`:

      MY_VERSION="1.2.3"

  Then the following search and replace patterns (including quotes) would be
  required:

```ini
[bumpversion:file:version.sh]
search = MY_VERSION="{current_version}"
replace = MY_VERSION="{new_version}"
```

## Command-line Options

Most of the configuration values above can also be given as an option on the command-line.
Additionally, the following options are available:

`--dry-run, -n`
  Don't touch any files, just pretend. Best used with `--verbose`.

`--allow-dirty`
  Normally, bumpversion will abort if the working directory is dirty to protect
  yourself from releasing unversioned files and/or overwriting unsaved changes.
  Use this option to override this check.

`--no-configured-files`
  Will not update/check files specified in the .bumpversion.cfg.
  Similar to dry-run, but will also avoid checking the files.
  Also useful when you want to update just one file with e.g.,
    `bump2version --no-configured-files major my-file.txt`

`--verbose`
  Print useful information to stderr

`--list`
  List machine readable information to stdout for consumption by other
  programs.

  Example output:

    current_version=0.0.18
    new_version=0.0.19

`-h, --help`
  Print help and exit

## Using bumpversion in a script

If you need to use the version generated by bumpversion in a script you can make use of
the `--list` option, combined with `grep` and `sed`.

Say for example that you are using git-flow to manage your project and want to automatically
create a release. When you issue `git flow release start` you already need to know the
new version, before applying the change.

The standard way to get it in a bash script is

    bump2version --dry-run --list <part> | grep <field name> | sed -r s,"^.*=",,

where `part` is as usual the part of the version number you are updating. You need to specify
`--dry-run` to avoid bumpversion actually bumping the version number.

For example, if you are updating the minor number and looking for the new version number this becomes

    bump2version --dry-run --list minor | grep new_version | sed -r s,"^.*=",,

## Using bumpversion to maintain a go.mod file within a Go project

In a module-aware Go project, when you create a major version of your module beyond v1, your module name will need
to include the major version # (e.g. `github.com/myorg/myproject/v2`).

You can use bump2version to maintain the major version # within the go.mod file by using the `parse` and `serialize`
options, as in this example:

- Example `.bumpversion.cfg` file:

```
    [bumpversion]
    current_version = 2.0.0
    commit = True

    [bumpversion:file:go.mod]
    parse = (?P<major>\d+)
    serialize = {major}
    search = module github.com/myorg/myproject/v{current_version}
    replace = module github.com/myorg/myproject/v{new_version}
```

- Example `go.mod` file:

```
    module github.com/myorg/myproject/v2

    go 1.12

    require (
        ...
    )
```

Then run this command to create version 3.0.0 of your project:

```
    bump2version --new-version 3.0.0 major
```
Your `go.mod` file now contains this module directive:

```
    module github.com/myorg/myproject/v3
```

## Development & Contributing

Thank you contributors! You can find a full list here: https://github.com/c4urself/bump2version/graphs/contributors

See also our [CONTRIBUTING.md](CONTRIBUTING.md)

Development of this happens on GitHub, patches including tests, documentation
are very welcome, as well as bug reports! Also please open an issue if this
tool does not support every aspect of bumping versions in your development
workflow, as it is intended to be very versatile.

## License

bump2version is licensed under the MIT License - see the [LICENSE.rst](LICENSE.rst) file for details
