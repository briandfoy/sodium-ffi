# NAME

Sodium::FFI - FFI implementation of libsodium

# SYNOPSIS

```perl
use strict;
use warnings;
use v5.34;

use Sodium::FFI ();

my $text = "1234";
my $padded = Sodium::FFI::pad($text, 16);
say Sodium::FFI::unpad($padded);
```

# DESCRIPTION

[Sodium::FFI](https://metacpan.org/pod/Sodium%3A%3AFFI) is a set of Perl bindings for the [LibSodium](https://doc.libsodium.org/)
C library. These bindings have been created using FFI via [FFI::Platypus](https://metacpan.org/pod/FFI%3A%3APlatypus) to make
building and maintaining the bindings easier than was done via [Crypt::NaCl::Sodium](https://metacpan.org/pod/Crypt%3A%3ANaCl%3A%3ASodium).
While we also intend to fix up [Crypt::NaCl::Sodium](https://metacpan.org/pod/Crypt%3A%3ANaCl%3A%3ASodium) so that it can use newer versions
of LibSodium, the FFI method is faster to build and release.

# Random Number Functions

LibSodium provides a few
[Random Number Generator Functions](https://doc.libsodium.org/generating_random_data)
to assist you in getting your data ready for encryption, decryption, or hashing.

## randombytes\_buf

```perl
use Sodium::FFI qw(randombytes_buf);
my $bytes = randombytes_buf(2);
say $bytes; # contains two bytes of random data
```

The [randombytes\_buf](https://doc.libsodium.org/generating_random_data#usage)
function returns string of random bytes limited by a provided length.

## randombytes\_buf\_deterministic

```perl
use Sodium::FFI qw(randombytes_buf_deterministic);
# create some seed string of length Sodium::FFI::randombytes_SEEDBYTES
my $seed = 'x' x Sodium::FFI::randombytes_SEEDBYTES;
# use that seed to create a random string
my $length = 2;
my $bytes = randombytes_buf_deterministic($length, $seed);
say $bytes; # contains two bytes of random data
```

The [randombytes\_buf\_deterministic](https://doc.libsodium.org/generating_random_data#usage)
function returns string of random bytes limited by a provided length.

It returns a byte string indistinguishable from random bytes without knowing the `$seed`.
For a given seed, this function will always output the same sequence.
The seed string you create should be `randombytes_SEEDBYTES` bytes long.
Up to 256 GB can be produced with a single seed.

## randombytes\_random

```perl
use Sodium::FFI qw(randombytes_random);
my $random = randombytes_random();
say $random;
```

The [randombytes\_random](https://doc.libsodium.org/generating_random_data#usage)
function returns an unpredictable value between `0` and `0xffffffff` (included).

## randombytes\_uniform

```perl
use Sodium::FFI qw(randombytes_uniform);
my $upper_limit = 0xffffffff;
my $random = randombytes_uniform($upper_limit);
say $random;
```

The [randombytes\_uniform](https://doc.libsodium.org/generating_random_data#usage)
function returns an unpredictable value between `0` and `$upper_bound` (excluded).
Unlike `randombytes_random() % $upper_bound`, it guarantees a uniform
distribution of the possible output values even when `$upper_bound` is not a
power of `2`. Note that an `$upper_bound` less than `2` leaves only a single element
to be chosen, namely `0`.

# Utility/Helper Functions

LibSodium provides a few [Utility/Helper Functions](https://doc.libsodium.org/helpers)
to assist you in getting your data ready for encryption, decryption, or hashing.

## sodium\_add

```perl
use Sodium::FFI qw(sodium_add);
my $left = "111";
$left = sodium_add($left, 111);
say $left; # bbb
```

The [sodium\_add](https://doc.libsodium.org/helpers#adding-large-numbers)
function adds 2 large numbers.

## sodium\_base642bin

```perl
use Sodium::FFI qw(sodium_base642bin);
say sodium_base642bin('/wA='); # \377\000
my $variant = Sodium::FFI::sodium_base64_VARIANT_ORIGINAL;
say sodium_base642bin('/wA=', $variant); # \377\000
$variant = Sodium::FFI::sodium_base64_VARIANT_ORIGINAL_NO_PADDING;
say sodium_base642bin('/wA', $variant); # \377\000
$variant = Sodium::FFI::sodium_base64_VARIANT_URLSAFE;
say sodium_base642bin('_wA=', $variant); # \377\000
$variant = Sodium::FFI::sodium_base64_VARIANT_URLSAFE_NO_PADDING;
say sodium_base642bin('_wA', $variant); # \377\000
```

The [sodium\_base642bin](https://doc.libsodium.org/helpers#base64-encoding-decoding)
function takes a base64 encoded string and turns it back into a binary string.

## sodium\_bin2base64

```perl
use Sodium::FFI qw(sodium_bin2base64);
say sodium_bin2base64("\377\000"); # /wA=
my $variant = Sodium::FFI::sodium_base64_VARIANT_ORIGINAL;
say sodium_bin2base64("\377\000", $variant); # /wA=
$variant = Sodium::FFI::sodium_base64_VARIANT_ORIGINAL_NO_PADDING;
say sodium_bin2base64("\377\000", $variant); # /wA
$variant = Sodium::FFI::sodium_base64_VARIANT_URLSAFE;
say sodium_bin2base64("\377\000", $variant); # _wA=
$variant = Sodium::FFI::sodium_base64_VARIANT_URLSAFE_NO_PADDING;
say sodium_bin2base64("\377\000", $variant); # _wA
```

The [sodium\_bin2base64](https://doc.libsodium.org/helpers#base64-encoding-decoding)
function takes a binary string and turns it into a base64 encoded string.

## sodium\_bin2hex

```perl
use Sodium::FFI qw(sodium_bin2hex);
my $binary = "ABC";
my $hex = sodium_bin2hex($binary);
say $hex; # 414243
```

The [sodium\_bin2hex](https://doc.libsodium.org/helpers#hexadecimal-encoding-decoding)
function takes a binary string and turns it into a hex string.

## sodium\_compare

```perl
use Sodium::FFI qw(sodium_compare);
say sodium_compare("\x01", "\x02"); # -1
say sodium_compare("\x02", "\x01"); # 1
say sodium_compare("\x01", "\x01"); # 0
```

The [sodium\_compare](https://doc.libsodium.org/helpers#comparing-large-numbers)
function compares two large numbers encoded in little endian format.
Results in `-1` when `$left < $right`
Results in `0` when `$left eq $right`
Results in `1` when `$left > $right`

## sodium\_hex2bin

```perl
use Sodium::FFI qw(sodium_hex2bin);
my $hex = "414243";
my $bin = sodium_hex2bin($hex);
say $bin; # ABC
```

The [sodium\_hex2bin](https://doc.libsodium.org/helpers#hexadecimal-encoding-decoding)
function takes a hex string and turns it into a binary string.

## sodium\_increment

```perl
use Sodium::FFI qw(sodium_increment);
my $x = "\x01";
$x = sodium_increment($x); # "\x02";
```

The [sodium\_increment](https://doc.libsodium.org/helpers#incrementing-large-numbers)
function takes an arbitrarily long unsigned number and increments it.

## sodium\_is\_zero

```perl
use Sodium::FFI qw(sodium_is_zero);
my $string = "\x00\x00\x01"; # zero zero 1
# entire string not zeros
say sodium_is_zero($string); # 0
# first byte of string is zero
say sodium_is_zero($string, 1); # 1
# first two bytes of string is zero
say sodium_is_zero($string, 2); # 1
```

The [sodium\_is\_zero](https://doc.libsodium.org/helpers#testing-for-all-zeros)
function tests a string for all zeros.

## sodium\_library\_minimal

```perl
use Sodium::FFI qw(sodium_library_minimal);
say sodium_library_minimal; # 0 or 1
```

The `sodium_library_minimal` function lets you know if this is a minimal version.

## sodium\_library\_version\_major

```perl
use Sodium::FFI qw(sodium_library_version_major);
say sodium_library_version_major; # 10
```

The `sodium_library_version_major` function returns the major version of the library.

## sodium\_library\_version\_minor

```perl
use Sodium::FFI qw(sodium_library_version_minor);
say sodium_library_version_minor; # 3
```

The `sodium_library_version_minor` function returns the minor version of the library.

## sodium\_memcmp

```perl
use Sodium::FFI qw(sodium_memcmp);
my $string1 = "abcdef";
my $string2 = "abc";
my $match_length = 3;
# string 1 and 2 are equal for the first 3
say sodium_memcmp($string1, $string2, $match_length); # 0
# they are not equal for 4 slots
say sodium_memcmp("abcdef", "abc", 4); # -1
```

The [sodium\_memcmp](https://doc.libsodium.org/helpers#constant-time-test-for-equality)
function compares two strings in constant time.
Results in `-1` when strings 1 and 2 aren't equal.
Results in `0` when strings 1 and 2 are equal.

## sodium\_pad

```perl
use Sodium::FFI qw(sodium_pad);
my $bin_string = "\x01";
my $block_size = 4;
say sodium_pad($bin_string, $block_size); # 01800000
```

The [sodium\_pad](https://doc.libsodium.org/padding) function adds
padding data to a buffer in order to extend its total length to a
multiple of the block size.

## sodium\_sub

```perl
use Sodium::FFI qw(sodium_sub);
my $x = "\x02";
my $y = "\x01";
my $z = sodium_sub($x, $y);
say $x; # \x01
```

The [sodium\_sub](https://doc.libsodium.org/helpers#subtracting-large-numbers)
function subtracts 2 large, unsigned numbers encoded in little-endian format.

## sodium\_unpad

```perl
use Sodium::FFI qw(sodium_unpad);
my $bin_string = "\x01\x80\x00\x00\x0";
my $block_size = 4;
say sodium_unpad($bin_string, $block_size); # 01
```

The [sodium\_unpad](https://doc.libsodium.org/padding) function
computes the original, unpadded length of a message previously
padded using `sodium_pad`.

## sodium\_version\_string

```perl
use Sodium::FFI qw(sodium_version_string);
say sodium_version_string; # 1.0.18
```

The `sodium_version_string` function returns the stringified version information
for the version of LibSodium that you have installed.

# COPYRIGHT

```
Copyright 2020 Chase Whitener. All rights reserved.
```

This library is free software; you can redistribute it and/or
modify it under the same terms as Perl itself.
