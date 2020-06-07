# build-debian-qemu-image

Script to bootstrapping qemu UEFI Debian images with debootstrap

See the blog post this came out of at <https://blog.entek.org.uk/technology/2020/06/06/building-debian-vms-with-debootstrap.html>

## Licence

Copyright 2020 Laurence Alexander Hurst

This file is part of build-debian-qemu-image.

build-debian-qemu-image is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

build-debian-qemu-image is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with build-debian-qemu-image.  If not, see <https://www.gnu.org/licenses/>.

See the file LICENCE in the original source code repository for the
full licence.

## Prerequisites

You will need:

* [debootstrap](https://packages.debian.org/search?keywords=debootstrap&searchon=names&suite=stable&section=all)
* [dosfstools](https://packages.debian.org/search?keywords=dosfstools&searchon=names&suite=stable&section=all)
* [qemu-utils](https://packages.debian.org/search?keywords=qemu-utils&searchon=names&suite=stable&section=all)

## Usage

```
Usage: build-debian-image [-hSDM] [-s suite] [-f file] [-z size] [-r passwd] [name]

-h:        Display this message and exit
-S:        Skip debootstrap initialising a blank image and go stright to
           mounting.  Requires an already setup image file as target.
-D:        Do debootstrap only and exit without chrooting and doing the
           stage 2 process.  As the debootstrap is the most time-consuming
           element, this can be helpful combined with -S during debugging and
           development.  The stage 2 script will still be written out.
-M:        Leave the NBD attached and chroot filesystems mounted at the end.
-s suite:  the Debian suite to build, defaults to buster (taken from
           first line matching /^deb / in /etc/apt/sources.list).
-f file:   filename for the image, defaults to <name>.qcow2
-z size:   Size of the image, in a format understood by the qemu-img command.
           Defaults to 5G, first 1GiB is entirely consumed by boot and swap
           partitions.
-w size:   Size of the image's swap partition in MiB (set to 0 to disable),
           defaults to 753
-r var:    Use passwd in var environment variable as the encrypted root
           password in the built image (avoids exposing the password via the
           command-line, which any user on the system can see).
-d domain: Use domain as the new hosts domain, defaults to my.domain.tld (
           taken from this host's 'hostname -d')
name:      hostname to configure the image to, defaults to debian-<suite>
```

## Examples

### Build a 5GiB image called debian-buster.qcow2 (assuming your host system is debian buster) with 753MiB swap, no root password configured, hostname of debian-buster and in the same domain as the host

```
build-debian-image
```

### Build a 10GiB Debian Jessie imaged called my-vm.img and a hostname of jessie-vm and no swap space

```
buil-debian-image -f my-vm.img -z 10G -w 0 -s jessie jessie-vm
```

### Build a 10GiB (default in all other regards) image but with a root password set

```
export ROOT_PWD='$6$salt$encrypted_sha512_passwd'
build-debian-image -z 10G -r ROOT_PWD
```

(note if you are using sudo you will need some jiggery-pokery to pass through the environment variable)

