=======
ramroot
=======

Run Linux entirely from RAM!  This project creates and enables a custom
mkinitcpio_ hook that completely loads the *root* (and */boot*)
filesystem into RAM during the initramfs_ boot stage.


Requirements
============

Arch Linux
    This package is designed specifically to work with the Arch Linux
    `boot process`_.  I suspect these scripts may work with slight
    modifications on other distributions as well, however this is
    currently untested.

sudo
    The ramroot script can be run as root or as a normal user.  If
    executed as a normal user, *sudo* will be called when necessary
    to modify or create initcpio files, and the user will be prompted
    for his or her sudo password.


Installation
============

This package is available in the AUR_ for easy installation, however the
ramroot script can also be run directly from a local git clone of this
repository.


Synopsis
========

``ramroot <action> [options]``


Description
===========

Ramroot can enable and disable the pre-userspace loading of the *root*
(and */boot*, if it exists) filesystem to RAM during system boot.

When ramroot is enabled, during the initial phase of boot the amount
of detected RAM on the computer along with the size the root filesystem
to be copied is shown on the screen.  If there is at least 500MB
more RAM than the size of the filesystem, the user will be prompted
to load the root filesystem to RAM.
(Confirmation prompt defaults to yes with a 15 second timeout.)

The size of the zram partition created is determined by taking the
size of the root filesystem plus half of the extra available RAM
(to a maximum of 6GB).

Other partitions will not be copied to RAM or mounted by default.
Usage of the *-F* option can be used to mount additional
partitions defined in */etc/fstab*.


Actions
=======

Action performed by ramroot; one must be specified.


``disable``
    Remove ramroot from */etc/mkinitcpio.conf* HOOKS.
    Remove ext4, vfat, and zram from */etc/mkinitcpio.conf* MODULES.
    Rebuild linux cpio boot image.

``enable``
    Rebuild  build and runtime hooks in */usr/lib/initcpio*.
    Add ramroot to */etc/mkinitcpio.conf* HOOKS.
    Add ext4, vfat, and zram to */etc/mkinitcpio.conf* MODULES.
    Rebuild linux cpio boot image.

``remove``
    Disable ramroot.
    Remove build and runtime hooks from */usr/lib/initcpio*.

``status``
    Print ramroot and root filesystem status to screen.
    Return exit status 0 if enabled and currently loaded to RAM.
    Return exit status 1 if enabled and not loaded RAM.
    Return exit status 2 if disabled.


Options
=======

All are optional (although *--root* may be required as noted below).

``-b, --boot <UUID>``
    Specify */boot* partition UUID to use when building hooks.

``-D, --dryrun``
    Execute action without making any changes.  Useful for debugging
    or viewing changes in *~/.cache/ramroot* before enabling.

``-F, --fstab``
    Mount all partitions (other than *root* and */boot*) in
    */etc/fstab* normally.

``-H, --help``
    Display help text and exit.

``-K, --keep``
    Keep copies of new build and runtime hooks in *~/.cache/ramroot*.

``-M, --message``
    Print 'Arch on RAM' message at tty login by using custom
    */etc/issue*.

``-N, --no``
    Change startup prompt default to not load filesystem to RAM.

``-r, --root <UUID>``
    Specify root partition UUID to use when building hooks;
    required if unable to detect UUID via lsblk or */etc/fstab*.

``-t, --timeout <SECONDS>``
    Set RAM boot prompt timeout (default=15).


Notes
=====

The filesystem transfer to RAM can take several minutes.  As soon as
the boot process is complete, the boot media can be safely removed.

Remember that all changes to files in RAM are completely lost once the
computer is reboot.  To update the system and edit files, boot the
device without transferring the filesystem to RAM.

Keep a clean and trimmed down system to maintain faster RAM sync times.
Arch Linux stores downloaded packages in */var/cache/pacman/pkg/*.
After every update, if no problems occur, consider removing
`old packages`_.  Consider installer fewer packages if you intend on
loading to RAM often.

Also, keep in mind that higher quality (more expensive) USB flash drives
often exhibit a dramatic improvement in RAM sync times.


Issues / Future Implementations
===============================

*   get UUIDs during boot

    +   more portable; UUIDs not hardcoded into initcpio hook

    +   no need to rebuild linux image with UUID changes

*   use *getopts* to easily allow grouping command line options together

*   *--rsync* option with *status=progress* (maybe use rsync
    automatically if installed)

*   option to create a unique hostname for each RAM boot instance

*   improve security features

    +   option to require removal of boot media before starting
        userspace

    +   option to fail boot unless loaded to RAM (requires *chroot* to
        make changes; advanced users)

*   *flush* action to sync the RAM filesystem back to the
    initial boot device

*   test on other distros


Credits
=======

This project was motivated greatly in part by the liveroot_ package and
by several inquisitive `forum posts`_.

:Author:
    Chris Magyar

:Version:
    1.1.8

:License:
    GPL 3.0

:Donate(xmr):
    41dUPANhvCvLUuRVJpUc9cRFnsLHzWiTPUhyuamrVwa61xoP
    uxZaD6R28cLqxEhTaC6LuwcHtkbUi2uELDD88MoQHJKePvP



.. _AUR: https://aur.archlinux.org/packages/ramroot/
.. _mkinitcpio: https://wiki.archlinux.org/index.php/mkinitcpio
.. _zram: https://en.wikipedia.org/wiki/Zram
.. _initramfs: https://en.wikipedia.org/wiki/Initial_ramdisk
.. _boot process: https://wiki.archlinux.org/index.php/Arch_boot_process
.. _build hook: https://wiki.archlinux.org/index.php/mkinitcpio#Build_hooks
.. _runtime hook: https://wiki.archlinux.org/index.php/mkinitcpio#Runtime_hooks
.. _HOOKS: https://wiki.archlinux.org/index.php/mkinitcpio#HOOKS
.. _MODULES: https://wiki.archlinux.org/index.php/mkinitcpio#MODULES
.. _arch-usb: http://valleycat.org/arch-usb/arch-usb.html
.. _old packages: https://wiki.archlinux.org/index.php/pacman#Cleaning_the_package_cache
.. _liveroot: https://github.com/bluerider/liveroot
.. _forum posts: https://bbs.archlinux.org/viewtopic.php?id=178963
