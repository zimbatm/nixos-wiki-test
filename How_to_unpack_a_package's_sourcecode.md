---
title: How to unpack a package's sourcecode
permalink: /How_to_unpack_a_package's_sourcecode/
---

To unpack a package into current directory, one may use following script.

    #!/bin/sh

    # Unpacks the target
    unpack () {
        local curSrc="$1"
        local friendlyName="$2"
        test -z "$curSrc" && return 2
        case "$curSrc" in
            *.tar) tar xvf $curSrc ;;
            *.tar.gz|*.tgz|*.tar.Z) gzip -d < $curSrc | tar xvf - ;;
            *.tar.bz2|*.tbz2) bzip2 -d < $curSrc | tar xvf - ;;
            *.zip) unzip $curSrc ;;
            *) if test -d "$curSrc" ; then
                    if test -n "$friendlyName" ; then
                        cp -prvd "$curSrc" "./$friendlyName"
                    else
                        cp -prvd "$curSrc" .
                    fi
                else
                    echo "source archive $curSrc has unknown type" >&2
                    return 1
                fi ;;
        esac
    }

    # Fetches the tarball into a store
    fetch () {
        nix-build $NIXPKGS_ALL -A $1.src --show-trace
    }

    unpack `fetch $1` $1

Copy it to a directory which is listed in the PATH, name it nix-unpack. Then, to obtain, say, Midnight Commander's sources, run:

    nix-unpack mc

Thanks goes to Mark Weber, Llu�s Batlle, Yury G. Kudrya