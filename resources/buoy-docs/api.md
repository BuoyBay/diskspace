---
title: "Disk space API"
summary: "Public diskspace types and operations with behavior and lifetime notes."
category: "Reference"
order: 20
keywords: ["diskspace", "API", "reference"]
symbols: ["DiskSpaceSupported", "DiskSpaceSnapshotSupported", "ReadDiskSpaceDetails", "DiskSpaceDetails", "DiskSpaceSnapshot", "DiskSpaceSnapshot.TotalBytes", "DiskSpaceSnapshot.FreeBytes", "DiskSpaceSnapshot.TotalNodes", "DiskSpaceSnapshot.FreeNodes", "DiskSpaceSnapshot.AvailableBytes", "DiskSpaceSnapshot.HasNodeCounts"]
related: ["index.md", "getting-started.md", "typed-api.md"]
---

# API reference

`Import diskspace` exposes the following public API. `DiskSpaceLinuxBackend`, `DiskSpaceWindowsBackend` and `DiskSpaceByteCounts` are internal modules, not public API. Properties described as read-only have no public setter.

| Symbol | Behavior |
| --- | --- |
| `DiskSpaceSupported() As Boolean` | Frozen: True only for the original macOS backend. |
| `DiskSpaceSnapshotSupported() As Boolean` | True on macOS, Linux and Windows targets. Compile-time; never touches a filesystem. |
| `DiskSpaceSnapshot(file As FileSystemItem)` | Read the filesystem containing an existing local file or directory; Nil is invalid. |
| `DiskSpaceSnapshot.TotalBytes As Integer` | Read-only filesystem capacity in bytes. |
| `DiskSpaceSnapshot.FreeBytes As Integer` | Read-only free byte count at capture time. |
| `DiskSpaceSnapshot.TotalNodes As Integer` | Read-only filesystem node capacity; -1 when `HasNodeCounts` is False. |
| `DiskSpaceSnapshot.FreeNodes As Integer` | Read-only free filesystem nodes at capture time; -1 when `HasNodeCounts` is False. |
| `DiskSpaceSnapshot.AvailableBytes As Integer` | Bytes the caller can write. macOS reports -1 if the volume does not provide it. |
| `DiskSpaceSnapshot.HasNodeCounts As Boolean` | False on Windows and on Linux filesystems that report no inodes. |
| `ReadDiskSpaceDetails(file As FileSystemItem) As DiskSpaceDetails` | Unsigned capacity for the volume containing `file`. macOS throws `InvalidArgumentException` if caller-available capacity is unreported. |
| `DiskSpaceDetails.TotalBytes / FreeBytes / AvailableBytes As UInt64` | Volume total (Windows: caller-scoped total), volume free, caller-available. |
| `DiskSpaceDetails.IsTotalCallerScoped As Boolean` | True on Windows, where `TotalBytes` follows the caller's quota. |
| `DiskSpaceDetails.HasNodeCounts As Boolean` | Whether node counts are reported. |
| `DiskSpaceDetails.TotalNodes / FreeNodes / AvailableNodes As Optional Of UInt64` | Node counts; `AvailableNodes` (`f_favail`) is Linux-only. |

## Errors and availability

macOS, Linux (glibc `statvfs`) and Windows (`GetDiskFreeSpaceExW`). Gate portable code on `DiskSpaceSnapshotSupported()`. `DiskSpaceSupported()` keeps its original macOS-only meaning and returns False on Linux and Windows. On Windows, `TotalBytes` is the caller's quota-scoped total and `FreeBytes` is volume-wide free space, and node counts are not reported (`HasNodeCounts` False, nodes -1). Missing or inaccessible paths raise `InvalidArgumentException`; on Linux and Windows its `ErrorCode` is the errno or Win32 error. Snapshot byte or node counts above 2^63 - 1 throw `OverflowException`; use `ReadDiskSpaceDetails` for unsigned values. Capacity is a point-in-time observation, not a reservation or a promise that a later write will succeed. Byte and node values can change immediately after the read. The immutable snapshot holds copied integers and needs no close operation.
## Continue reading

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)

[Full typed API: properties, parameters, defaults, and returns](typed-api.md)
