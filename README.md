# build-asuswrt-merlin

Helper scripts to build [RMerl/asuswrt-merlin](https://github.com/RMerl/asuswrt-merlin) without much extra typing.

* Backlink to [Compiling from source using a Debian based Linux Distribution](https://github.com/RMerl/asuswrt-merlin/wiki/Compiling-from-source-using-a-Debian-based-Linux-Distribution)

## Summary

Last tested on `master` from AlteredCarrot71/asuswrt-merlin as of 2019-04-26. The script takes care of the steps mentioned above for Ubuntu 13.10 and newer. In addition it does fixups to the source tree which ensure that it can run without superuser rights as long as the user makes sure all the packages are installed. It will also check for the packages being installed and bail out with a meaningful error message if not.

It can be run in two modes: with or without `sudo` involved. With `sudo` it will use the symbolic link method inside `/opt`, whereas without it will modify the files in the source tree to adjust hardcoded paths.

If you are running inside a [`tmux`](http://tmux.sourceforge.net/) pane, this will also pipe the output into a log file (see _Environment variables_).

### Supported distributions

* Ubuntu 14.04 and above (tested on Ubuntu 14.04 i386, 16.04 i386, 16.04 x86_64 and 18.04 x86_64)

## Syntax

* Unprivileged: `./debian-build-image <router-model> [path-to-asuswrt-merlin]`
* With `sudo`: `USE_SUDO=1 ./debian-build-image <router-model> [path-to-asuswrt-merlin]`

You can leave out the `path-to-asuswrt-merlin` argument and the script will check the folder it resides in for a marker file (`README-merlin.txt`) to see whether this is the expected source tree.

### Supported router models

The \*R and \*W variants of the routers are also supported. R is for retail versions and W for white models. The firmware is the same. The router name can be given in upper or lower case and you can leave out the trailing U, R or W.

#### MIPS-based routers
* RT-N66U
* RT-AC66U

### Environment variables

Some environment variables influence how the script behaves:

* Any non-empty value for `USE_SUDO` will cause the image creation process to assume that `sudo` is available to symlink the toolchain into `/opt`. This is useful if for some reason the unprivileged method fails or if you simply prefer to stick closer to the process explained on the RMerl/asuswrt-merlin wiki.
* Any non-empty value for `FORCE_UNSUPPORTED` can be used to run the script on otherwise not explicitly supported Debian flavors.
* `TMUX_PANE` is used to sense the presence of a `tmux` session and will turn on logging. To inhibit this behavior, make sure to either `unset` this variable or to give it an empty value during invocation (i.e. `TMUX_PANE= ./debian-build-image` ...).

You can either set the environment variable globally by exporting it prior to the invocation, e.g.:

```bash
export USE_SUDO=1
./debian-build-image RT-N66U
```

... or by passing it on the command line like so:

```bash
USE_SUDO=1 ./debian-build-image RT-N66U
```

### Special syntax for prerequisites

In order to have the script install all the prerequisites using `apt-get`, use the following method:

* `./debian-build-image -p <router-model>` (or `--prereq` instead of `-p`)

Please note that this requires you to be a sudoer. Usually that means you need to be a member of the group `sudo` on `.deb`-based distros or `wheel` on `.rpm`-based distros.

**NOTE:** Strictly speaking the command should also work without the router model given, but it may miss some model-specific prerequisites in such a case. As an example: ARM routers require `libelf1:i386` because the `gcc` of the ARM toolchain depends on it.

## Usage in Ubuntu

### Preparation

* Verify you have `git` installed, otherwise do `sudo apt-get --no-install-recommends install git git-man`.
* Change into a folder inside of which you'd like the repository to reside (e.g. `~`).
* Clone the AlteredCarrot71/asuswrt-merlin source:
  * via SSH: `git clone git@github.com:AlteredCarrot71/asuswrt-merlin.git`
  * via HTTPS: `git clone https://github.com/AlteredCarrot71/asuswrt-merlin.git`
* Clone this repository or download the raw `debian-build-image` script:
  * via SSH: `git clone git@github.com:AlteredCarrot71/build-asuswrt-merlin.git`
  * via HTTPS: `git clone https://github.com/AlteredCarrot71/build-asuswrt-merlin.git`
  * Download `wget https://raw.githubusercontent.com/AlteredCarrot71/build-asuswrt-merlin/master/debian-build-image && chmod +x debian-build-image`
* Copy `debian-build-image` into the clone of AlteredCarrot71/asuswrt-merlin (e.g. `~/asuswrt-merlin`).
* Change into the AlteredCarrot71/asuswrt-merlin directory (e.g. `~/asuswrt-merlin`) and invoke `./debian-build-image --prereq` there.
  * This will prompt you to execute a `sudo` command to install all prerequisites. This command does not check before whether some packages are missing, though. Remember that this requires you to be a sudoer!

### Building an image

Well, the syntax has been explained above. Running the script is as easy as invoking (from our previous example):

    ~/asuswrt-merlin/debian-build-image RT-N66U

The name of the router is case-insensitive. Also, it handles the models with their U, R, and W prefixes.

At this point you'll probably want to take a break and come back later. This will take quite a while.

### Starting over

* Change into the clone directory
* You'll want to issue a `git reset --hard` inside the cloned repository to undo all the changes `debian-build-image` does
* And then start over after the preparation steps above

## Usage with Vagrant

It should be the easiest way to build firmware in virtual machine using [Vagrant](http://www.vagrantup.com/). In order to use it, you could install [Virtualbox](https://www.virtualbox.org/) provider.

First run a virtual machine:

    vagrant up

Second look for some firmware release version on [releases page](https://github.com/AlteredCarrot71/asuswrt-merlin/releases) and build an image for desired router model:

    vagrant ssh -- compile-image -m RT-N66U -r 378.56_2

You can build firmware from any Git branch, for example 'master':

    vagrant ssh -- compile-image -m RT-N66U -r master

Then wait for cloning sources from GitHub into VM, building an image and copying it to `release/` directory.

At the end of building, stop VM:

    vagrant halt

and remove it completely:

    vagrant destroy

## Contribute

Please contribute by reporting issues and sending pull requests.

Oh, and feel free to fork or embed this into your repository.

## License

This work is released into the public domain. See LICENSE. If _public domain_ is not a recognized legal concept in your jurisdiction, treat as [CC0](https://creativecommons.org/publicdomain/zero/1.0/); which should have the proper wording for many jurisdictions. The disclaimer from `LICENSE` (second to last paragraph) still applies.

If neither of these suite your needs, please ask by opening a ticket.
