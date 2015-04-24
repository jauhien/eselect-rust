# [eselect](https://wiki.gentoo.org/wiki/Project:Eselect) module for managing Rust compiler symlinks

## User's guide

**eselect-rust** allows you to easily switch between different Rust implementations.

List available implementations:

```
eselect rust list
```

Set implementation you need:

```
eselect rust set TARGET
```

where `TARGET` is the target number or name as listed in the `list` command.

Unset symlinks:

```
eselect rust unset
```

## Packager's guide

Every package that installs Rust implementation on Gentoo should collaborate with this eselect module.

### Target and target suffix

To register your package in eselect-rust you need to install `/etc/env.d/rust/provider-TARGET` file,
where TARGET is your target name as it will be show by eselect.

To differentiate symlink targets installed by different packages target suffix is used.
Target suffix is the part of target name that follows first `-` or target name itself if it includes no `-` characters.
To split out target suffix, command `cut -d- -f2-` is used. Some examples:

```
TARGET                  TARGET SUFFIX

rust-1.0.0_beta2        1.0.0_beta
rust-bin-1.0.0_beta2    bin-1.0.0_beta2
somecrazyimpl           somecrazyimpl
```

### Symlinks

Rust implementation package can include a number of symlinks that can be managed by eselect.
Package itself should install only symlink targets named `PATH/NAME-TARGET_SUFFIX`.
Where `PATH/NAME` is the future symlink location and `TARGET_SUFFIX` is described in the previous paragraf.

*Example*. If package wants to have symlinks `/usr/bin/rustc` and `/usr/bin/rustdoc`
and its target suffix is `1.0.0_beta`, it should install these files:

```
/usr/bin/rustc-1.0.0_beta
/usr/bin/rustdoc-1.0.0_beta
```

The main symlink that **eselect-rust** manages is `/usr/bin/rustc`.
It is the symlink that every package should have and it determines what implementation is shown as active by eselect.

To determine other symlinks that package sets, **eselect-rust** uses the content of the `/etc/env.d/rust/provider-TARGET` file.
This file should contain symlink paths one per line. If this file is empty, **eselect-rust**
behaves as its content were one line `/usr/bin/rustdoc`.

*Example*. If you want to have symlinks `/usr/bin/rustc`, `/usr/bin/rustdoc` and `/usr/bin/rust-gdb`,
then `/etc/env.d/rust/provider-TARGET` should contain:

```
/usr/bin/rustdoc
/usr/bin/rust-gdb
```

Note, that `/usr/bin/rustc` should not be listed, as it is always managed by eselect.

### Notes

**eselect-rust** automatically prepends every path with `${EROOT}` variable.
