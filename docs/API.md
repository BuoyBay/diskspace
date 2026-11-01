# Filesystem capacity · 0.1.0

`Import diskspace`. `New DiskSpaceSnapshot(file)` returns read-only signed `TotalBytes`, `FreeBytes`, `AvailableBytes`, `TotalNodes` and `FreeNodes` for the filesystem containing an existing path, plus `HasNodeCounts`. `ReadDiskSpaceDetails(file)` returns the same capacity as unsigned `UInt64` values with explicit scopes. Values are OS snapshots, not reservations or promises that a future write will succeed. Nothing is written and nothing prompts.

| Value | macOS | Linux (`statvfs`) | Windows (`GetDiskFreeSpaceExW`) |
| --- | --- | --- | --- |
| `TotalBytes` | `NSFileSystemSize` | `f_blocks × f_frsize` | `TotalNumberOfBytes`: the caller's quota-scoped total (`IsTotalCallerScoped`) |
| `FreeBytes` | `NSFileSystemFreeSize` | `f_bfree × f_frsize` (includes root reserve) | `TotalNumberOfFreeBytes`: volume-wide |
| `AvailableBytes` | `NSURLVolumeAvailableCapacityKey` (snapshot: -1 if unreported) | `f_bavail × f_frsize` | `FreeBytesAvailableToCaller` (quota-aware) |
| Node counts | `NSFileSystemNodes` / `FreeNodes` | `f_files` / `f_ffree` / `f_favail`; absent when `f_files` is 0 (btrfs) | not reported: `HasNodeCounts` False, nodes -1 |

On Windows a per-user quota can make `TotalBytes` smaller than the volume and `FreeBytes` larger than `TotalBytes`; the two are never mixed into one number. A file path is measured through its directory.

`DiskSpaceSupported()` is frozen to its original meaning: True only on macOS. Portable code gates on `DiskSpaceSnapshotSupported()`. Snapshot values above 2^63 - 1 throw `OverflowException` on Linux and Windows instead of wrapping; `ReadDiskSpaceDetails` keeps the full unsigned range. An unmeasurable path throws `InvalidArgumentException` whose `ErrorCode` is the errno or Win32 error.

Linux and Windows backends live in internal modules (`DiskSpaceLinuxBackend`, `DiskSpaceWindowsBackend`, `DiskSpaceByteCounts`). Their declarations are copied from the container interop modules (`linuxfilesystem.ReadSpace`, `WinFiles.ReadDiskSpace`); the package imports no container code. FSID, mount flags and raw block counts stay in those legacy modules.

Maintainers run the private validation suite before release. See the repository README for public build instructions.

Inspired by [macoslib’s Cocoa examples](https://github.com/macoslib/macoslib); implemented independently against [Apple’s API](https://developer.apple.com/documentation/foundation/filemanager/attributesoffilesystem(forpath:)).
