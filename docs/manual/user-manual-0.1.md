# User Manual

- [kawashima](#kawashima)
- [hcaenc](#hcaenc)
- [hcacc](#hcacc)

## kawashima

[kawashima](https://github.com/Hozuki/kawashima) is a library to decode [CRI HCA audio](http://www.criware.com/en/products/adx2.html).
It is named after [Mizuki Kawashima](http://www.project-imas.com/wiki/Mizuki_Kawashima).

Its original code is from [here](https://mega.co.nz/#!Fh8FwKoB!0xuFdrit3IYcEgQK7QIqFKG3HMQ6rHKxrH3r5DJlJ3M), in public
domain. Some adaptations are made to pack the functions into a shared library.

For how-tos, please see the [API docs](../api/ks-api.md) or
[example project](https://github.com/Hozuki/libcgss/tree/master/src/apps/hca2wav/hca2wav.cpp).

Beware that, if you want to integrate the library or any piece of code, **DO AT YOUR OWN RISK**.

## hcaenc

**hcaenc** is used to encode an HCA audio file from a WAVE audio file.
Internally it calls `hcaencEncodeToFile` API in `hcaenc_lite.dll`
included in [ADX2LE](http://www.adx2le.com) suite.

Usage:

```cmd
hcaenc.exe <input WAVE> <output HCA>
```

Example:

```cmd
hcaenc.exe input.wav output.hca
```

Please note that the HCA encoded by `hcaenc_lite.dll` is always statically
encrypted (cipher type 1). Its sampling rate is fixed at `44100` Hz.

Due to copyright reasons, `hcaenc_lite.dll` is not supplied in this repository.
Please download ADX2LE suite manually.

## hcacc

**hcacc** is a utility that can convert HCA files from one cipher type to
another.

Usage:

```cmd
hcacc.exe <input HCA> <output HCA> [-ot <output HCA cipher type>]
          [-i1 <input HCA key 1 (if necessary)>] [-i2 <input HCA key 2 (if necessary)>]
          [-o1 <output HCA key 1>] [-o2 <output HCA key 2>]
```

The valid cipher types are:

- `0` (not encrypted);
- `1` (statically encrypted);
- `56` (encrypted with a key pair).

The cipher type of the input HCA file is automatically detected, and it cannot be
set by code.

Example:

```cmd
hcacc.exe input.hca output.hca -ot 0
hcacc.exe input.hca output.hca -ot 56 -o1 AABBCCDD -o2 01020304
```

Assume we are using an HCA file encoded by **hcaenc** (or `hcaenc_lite.dll`),
and this command will convert the whole `input.hca` from cipher type 1 to
cipher type 0, and write to the new file `output.hca`.

This utility is extremly useful when creating HCA files for games. Since
`hcaenc_lite.dll` is hardcoded as type 1-only, the encoded HCA may not be
accepted by games that use type 56 (which also have independent key pairs)
or type 0 HCA audio files. A little conversion should do the magic and
solve this problem.