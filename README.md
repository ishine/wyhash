Simple Is Best
====

wyhash and wyrand are the ideal 64-bit hash function and PRNG respectively: solid, portable, fastest, simplest. wyhash passed SMHasher, wyrand passed BigCrush, practrand and designed to drop-in replace C rand(). See our [manuscript](manuscript.md). Currently wyhash is a hasher by default in a hash table in the great Zig language.

wyhash_v3
----

Dear users, wyhash now upgrades to version 3. 

* v3 provides double bulk speed compared to v2.

* v3 provides extra 60% speed for dynamic length short key compared to v2.

* v3 code size have been halfed. The big switch disappears, thanks to xxh3's idea for finalization.

* v3 comes up with the wyhash32 function that is 32-bit-machine efficient. Ideal for real applications.

It closes to the perfection. Cheers!


----------------------------------------

wyrand code:
```C
inline	uint64_t	wyrand(uint64_t	*seed){    
	*seed+=0xa0761d6478bd642full;    
	__uint128_t	t=(__uint128_t)(*seed^0xe7037ed1a0b428dbull)*(*seed);    
	return	(t>>64)^t;    
}
```

Benchmarks
----

https://github.com/rurban/smhasher


|key size | wyhash | t1ha2_atonce |
| ---- | ---- | ---- |
|bulk:bytes/cycle|5.222 | 4.393|
|small:cycles/hash|25.103 |45.513|
|hashmap:cycles/op|176.384|198.052|

```
--- Testing wyhash "wyhash v3 (portable, 64-bit, little-endian)" GOOD

[[[ Speed Tests ]]]

Bulk speed test - 262144-byte keys
Alignment  7 -  5.217 bytes/cycle - 14925.35 MiB/sec @ 3 ghz
Alignment  6 -  5.217 bytes/cycle - 14925.25 MiB/sec @ 3 ghz
Alignment  5 -  5.205 bytes/cycle - 14890.80 MiB/sec @ 3 ghz
Alignment  4 -  5.212 bytes/cycle - 14911.61 MiB/sec @ 3 ghz
Alignment  3 -  5.217 bytes/cycle - 14927.32 MiB/sec @ 3 ghz
Alignment  2 -  5.213 bytes/cycle - 14914.75 MiB/sec @ 3 ghz
Alignment  1 -  5.212 bytes/cycle - 14910.51 MiB/sec @ 3 ghz
Alignment  0 -  5.281 bytes/cycle - 15108.72 MiB/sec @ 3 ghz
Average      -  5.222 bytes/cycle - 14939.29 MiB/sec @ 3 ghz

Small key speed test -    1-byte keys -    22.67 cycles/hash
Small key speed test -    2-byte keys -    23.00 cycles/hash
Small key speed test -    3-byte keys -    23.00 cycles/hash
Small key speed test -    4-byte keys -    19.63 cycles/hash
Small key speed test -    5-byte keys -    31.00 cycles/hash
Small key speed test -    6-byte keys -    31.00 cycles/hash
Small key speed test -    7-byte keys -    31.00 cycles/hash
Small key speed test -    8-byte keys -    19.00 cycles/hash
Small key speed test -    9-byte keys -    32.00 cycles/hash
Small key speed test -   10-byte keys -    32.00 cycles/hash
Small key speed test -   11-byte keys -    32.00 cycles/hash
Small key speed test -   12-byte keys -    21.99 cycles/hash
Small key speed test -   13-byte keys -    21.99 cycles/hash
Small key speed test -   14-byte keys -    21.99 cycles/hash
Small key speed test -   15-byte keys -    21.99 cycles/hash
Small key speed test -   16-byte keys -    21.99 cycles/hash
Small key speed test -   17-byte keys -    24.00 cycles/hash
Small key speed test -   18-byte keys -    24.00 cycles/hash
Small key speed test -   19-byte keys -    24.00 cycles/hash
Small key speed test -   20-byte keys -    24.00 cycles/hash
Small key speed test -   21-byte keys -    24.00 cycles/hash
Small key speed test -   22-byte keys -    24.00 cycles/hash
Small key speed test -   23-byte keys -    24.00 cycles/hash
Small key speed test -   24-byte keys -    24.00 cycles/hash
Small key speed test -   25-byte keys -    25.65 cycles/hash
Small key speed test -   26-byte keys -    25.64 cycles/hash
Small key speed test -   27-byte keys -    25.63 cycles/hash
Small key speed test -   28-byte keys -    25.00 cycles/hash
Small key speed test -   29-byte keys -    26.00 cycles/hash
Small key speed test -   30-byte keys -    26.00 cycles/hash
Small key speed test -   31-byte keys -    26.00 cycles/hash
Average                                    25.103 cycles/hash
```

https://github.com/leo-yuriev/t1ha
```
Bench for tiny keys (7 bytes):
t1ha2_atonce            :     16.953 cycle/hash,  2.422 cycle/byte,  0.413 byte/cycle,  1.239 GiB/s @3.0GHz roughly
t1ha2_atonce128*        :     33.344 cycle/hash,  4.763 cycle/byte,  0.210 byte/cycle,  0.630 GiB/s @3.0GHz roughly
t1ha2_stream*           :     85.875 cycle/hash, 12.268 cycle/byte,  0.082 byte/cycle,  0.245 GiB/s @3.0GHz roughly
t1ha2_stream128*        :    105.875 cycle/hash, 15.125 cycle/byte,  0.066 byte/cycle,  0.198 GiB/s @3.0GHz roughly
t1ha1_64le              :     16.828 cycle/hash,  2.404 cycle/byte,  0.416 byte/cycle,  1.248 GiB/s @3.0GHz roughly
t1ha0                   :     14.102 cycle/hash,  2.015 cycle/byte,  0.496 byte/cycle,  1.489 GiB/s @3.0GHz roughly
xxhash32                :     18.703 cycle/hash,  2.672 cycle/byte,  0.374 byte/cycle,  1.123 GiB/s @3.0GHz roughly
xxhash64                :     22.062 cycle/hash,  3.152 cycle/byte,  0.317 byte/cycle,  0.952 GiB/s @3.0GHz roughly
StadtX                  :     18.750 cycle/hash,  2.679 cycle/byte,  0.373 byte/cycle,  1.120 GiB/s @3.0GHz roughly
HighwayHash64_pure_c    :    584.000 cycle/hash, 83.429 cycle/byte,  0.012 byte/cycle,  0.036 GiB/s @3.0GHz roughly
HighwayHash64_portable  :    483.000 cycle/hash, 69.000 cycle/byte,  0.014 byte/cycle,  0.043 GiB/s @3.0GHz roughly
HighwayHash64_sse41     :     70.000 cycle/hash, 10.000 cycle/byte,  0.100 byte/cycle,  0.300 GiB/s @3.0GHz roughly
wyhash                  :     13.266 cycle/hash,  1.895 cycle/byte,  0.528 byte/cycle,  1.583 GiB/s @3.0GHz roughly

Bench for large keys (16384 bytes):
t1ha2_atonce            :   3552.000 cycle/hash,  0.217 cycle/byte,  4.613 byte/cycle, 13.838 GiB/s @3.0GHz roughly
t1ha2_atonce128*        :   3538.000 cycle/hash,  0.216 cycle/byte,  4.631 byte/cycle, 13.893 GiB/s @3.0GHz roughly
t1ha2_stream*           :   3614.000 cycle/hash,  0.221 cycle/byte,  4.533 byte/cycle, 13.600 GiB/s @3.0GHz roughly
t1ha2_stream128*        :   3612.000 cycle/hash,  0.220 cycle/byte,  4.536 byte/cycle, 13.608 GiB/s @3.0GHz roughly
t1ha1_64le              :   3637.000 cycle/hash,  0.222 cycle/byte,  4.505 byte/cycle, 13.514 GiB/s @3.0GHz roughly
t1ha0                   :   1308.000 cycle/hash,  0.080 cycle/byte, 12.526 byte/cycle, 37.578 GiB/s @3.0GHz roughly
xxhash32                :   8296.000 cycle/hash,  0.506 cycle/byte,  1.975 byte/cycle,  5.925 GiB/s @3.0GHz roughly
xxhash64                :   4227.000 cycle/hash,  0.258 cycle/byte,  3.876 byte/cycle, 11.628 GiB/s @3.0GHz roughly
StadtX                  :   3750.000 cycle/hash,  0.229 cycle/byte,  4.369 byte/cycle, 13.107 GiB/s @3.0GHz roughly
HighwayHash64_pure_c    :  46788.000 cycle/hash,  2.856 cycle/byte,  0.350 byte/cycle,  1.051 GiB/s @3.0GHz roughly
HighwayHash64_portable  :  41933.000 cycle/hash,  2.559 cycle/byte,  0.391 byte/cycle,  1.172 GiB/s @3.0GHz roughly
HighwayHash64_sse41     :   6074.000 cycle/hash,  0.371 cycle/byte,  2.697 byte/cycle,  8.092 GiB/s @3.0GHz roughly
wyhash                  :   3496.000 cycle/hash,  0.213 cycle/byte,  4.686 byte/cycle, 14.059 GiB/s @3.0GHz roughly
```

----------------------------------------

https://github.com/lemire/testingRNG

| PRNG |  cycles per byte |
| ---- | ---- |
| xorshift_k4 | 1.60 |
| xorshift_k5 | 1.77 |
| mersennetwister | 2.41 |
| mitchellmoore | 3.12 |
| widynski | 1.88 |
| xorshift32 | 2.07 |
| pcg32 | 1.72 |
| rand | 5.04 |
| aesdragontamer | 0.71 |
| aesctr | 0.83 |
| lehmer64 | 0.86 |
| xorshift128plus | 0.85 |
| xoroshiro128plus | 0.96 |
| splitmix64 | 0.85 |
| pcg64 | 1.16 |
| xorshift1024star | 1.28 |
| xorshift1024plus | 0.91 |
| wyrand | 0.67 |

----------------------------------------

Lemire:[The fastest conventional random number generator that can pass Big Crush?](https://lemire.me/blog/2019/03/19/the-fastest-conventional-random-number-generator-that-can-pass-big-crush/)


| PRNG | ns/rand | vs wyrand |
| ---- | ---- | ---- |
| wyrand | 0.792 | 100.000% |
| lehmer64 | 1.219 | 153.858% |
| 3-lehmer64 | 0.816 | 103.010% |
| splitmix64 | 1.220 | 154.059% |
| 3-splitmix64 | 1.134 | 143.214% |
| xoshiro256 | 3.069 | 387.460% |
| pcg64 | 2.559 | 323.004% |
| pcg32 | 1.320 | 166.617% |

----------------------------------------

I thank these names:

Reini Urban

Dietrich Epp

Joshua Haberman

Tommy Ettinger

Daniel Lemire

Otmar Ertl

cocowalla

leo-yuriev

Diego Barrios Romero

paulie-g 

dumblob

Yann Collet

