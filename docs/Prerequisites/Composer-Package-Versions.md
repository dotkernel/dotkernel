# Composer Package Versions

- [Composer Package Versions](#composer-package-versions)
    - [Valid versions](#valid-versions)
    - [Range Versions](#range-versions)
    - [Next Significant Version](#next-significant-version)
        - [Tilde Operator](#tilde-operator)
        - [Caret Operator](#caret-operator)

Versions follow the format of `X.Y.Z` or `vX.Y.Z` with an optional suffix of `-dev`, `-patch`, `-alpha`, `-beta` or `-RC`. The patch, alpha, beta and RC suffixes can also be followed by a number.
> It's called [Semver](http://semver.org/), which stands for semantic versioning.

- Exact version `1.0.2`
- Exact version or another exact version `1.0.2 | 1.0.3`
- Wildcard patch Version `1.0.*`
- Version from git branches, format: `{branch-name}-version`, eg.: `feature-branch-2.0.x-dev`

## Valid versions

```txt
1.0.0
1.0.2
1.1.0
0.2.5
1.0.0-dev
1.0.0-alpha3
1.0.0-beta2
1.0.0-RC5
```

## Range Versions

- `>=1.0`  - version 1.0 or greater
- `>=1.0,<2.0` - version `1.0` or greater, but lower than `2.0`
- `>=1.0,<1.1 | >=1.2` - version `1.0`, but lower than `1.1` OR version `1.2` or greater

## Next Significant Version

### Tilde Operator

- `~4.1.3` means `>=4.1.3,<4.2.0`
- `~4.1` means `>=4.1.0,<5.0.0` (most used),
- `~0.4` means `>=0.4.0,<1.0.0`,
- `~4` means `>=4.0.0,<5.0.0`.

### Caret Operator

- `^4.1.3` (most used) means >=4.1.3,<5.0.0,
- `^4.1` means `>=4.1.0,<5.0.0`, same as ~4.1 but:
- `^0.4` means `>=0.4.0,<0.5.0`, this is different from `~0.4` and is more useful for defining backwards compatible version ranges.
- `^4` means `>=4.0.0,<5.0.0` which is the same as ~4 and 4.*.

Sources:
[Understanding Composer Versions](http://qpleple.com/understand-composer-versions/)
[Tilde and Caret Constraints](https://blog.madewithlove.be/post/tilde-and-caret-constraints/)
