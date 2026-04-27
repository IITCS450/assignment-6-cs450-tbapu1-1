# Assignment 6 Write-up: Symbolic Links

## Summary

This solution adds symbolic link support to xv6-public. It introduces a new inode type, wires a new `symlink(target, linkpath)` system call through the user/kernel syscall path, stores symlink targets as NUL-terminated strings in the symlink inode data blocks, and updates `open()` to follow symlinks.

## Implementation Details

- Added `T_SYMLINK` as inode type `4` in `fs.h` and `stat.h`.
- Added syscall number `SYS_symlink`, user prototype, assembly syscall stub, and syscall table entry.
- Implemented `sys_symlink()` in `sysfile.c` by creating `linkpath` as a `T_SYMLINK` inode and writing the target path string into the inode.
- Updated `sys_open()` to detect `T_SYMLINK`, read the stored target, resolve it with `namei()`, and continue following links.
- Added a maximum symlink-follow depth of 10 to prevent infinite loops.
- Added `testsymlink.c` and included `_testsymlink` in `UPROGS`.

## Testing

I built xv6 and `fs.img` in a Linux Docker container using an i686 cross-compiler. I also booted xv6 with QEMU and ran:

```sh
testsymlink
```

The test output was:

```text
PASS: symlink created
PASS: read through symlink
PASS: loop detected (open failed)
testsymlink done
```
