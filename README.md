# RiteLabs' Fx Hash

**A tiny, fast, zero-dep and no_std fxhash fork. More up to date.**

This hashing algorithm was extracted from the Rustc compiler.  This is the same hashing algorithm used for some internal operations in Firefox.  The strength of this algorithm is in hashing 8 bytes at a time on 64-bit platforms, where the FNV algorithm works on one byte at a time.

## Disclaimer

It is **not a cryptographically secure** hash, so it is strongly recommended that you do not use this hash for cryptographic purproses.  Furthermore, this hashing algorithm was not designed to prevent any attacks for determining collisions which could be used to potentially cause quadratic behavior in `HashMap`s.  So it is not recommended to expose this hash in places where collissions or DDOS attacks may be a concern.

## Examples

A builder for default Fx hashers.

```rust
use std::hash::BuildHasherDefault;

pub type FxBuildHasher = BuildHasherDefault<FxHasher>;
```

Building an Fx backed hashmap.

```rust
pub type FxHashMap<K, V> = HashMap<K, V, FxBuildHasher>;

let mut hashmap = FxHashMap::default();

hashmap.insert("black", 0);
hashmap.insert("white", 255);
```

Building an Fx backed hashset.

```rust
pub type FxHashSet<K, V> = HashSet<K, V, FxBuildHasher>;

let mut hashset = FxHashSet::default();

hashset.insert("black");
hashset.insert("white");
```

## Benchmarks

Generally `fxhash` is faster than `fnv` on `u32`, `u64`, or any byte sequence with length >= 5.  However, keep in mind that hashing speed is not the only characteristic worth considering.  That being said, Rustc had an observable increase in speed when switching from `fnv` backed hashmaps to `fx` based hashmaps.

    test bench_fnv_003     ... bench:           1 ns/iter (+/- 0)
    test bench_fnv_004     ... bench:           2 ns/iter (+/- 0)
    test bench_fnv_011     ... bench:           6 ns/iter (+/- 0)
    test bench_fnv_012     ... bench:           6 ns/iter (+/- 0)
    test bench_fnv_023     ... bench:          14 ns/iter (+/- 1)
    test bench_fnv_024     ... bench:          14 ns/iter (+/- 0)
    test bench_fnv_068     ... bench:          61 ns/iter (+/- 4)
    test bench_fnv_132     ... bench:         132 ns/iter (+/- 43)
    test bench_fx_003      ... bench:           1 ns/iter (+/- 0)
    test bench_fx_004      ... bench:           1 ns/iter (+/- 0)
    test bench_fx_011      ... bench:           2 ns/iter (+/- 0)
    test bench_fx_012      ... bench:           2 ns/iter (+/- 0)
    test bench_fx_023      ... bench:           3 ns/iter (+/- 0)
    test bench_fx_024      ... bench:           3 ns/iter (+/- 0)
    test bench_fx_068      ... bench:           5 ns/iter (+/- 0)
    test bench_fx_132      ... bench:          12 ns/iter (+/- 1)
    test bench_seahash_003 ... bench:          29 ns/iter (+/- 0)
    test bench_seahash_004 ... bench:          29 ns/iter (+/- 0)
    test bench_seahash_011 ... bench:          26 ns/iter (+/- 0)
    test bench_seahash_012 ... bench:          27 ns/iter (+/- 1)
    test bench_seahash_023 ... bench:          27 ns/iter (+/- 1)
    test bench_seahash_024 ... bench:          28 ns/iter (+/- 4)
    test bench_seahash_068 ... bench:          31 ns/iter (+/- 3)
    test bench_seahash_132 ... bench:          35 ns/iter (+/- 1)
