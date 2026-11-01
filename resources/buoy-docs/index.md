---
title: "Disk space"
summary: "Inspect filesystem byte and node capacity for an existing local path."
category: "Overview"
order: 0
keywords: ["diskspace", "macOS", "Linux", "Windows", "native"]
related: ["getting-started.md", "api.md", "typed-api.md"]
---

# Disk space

Use `diskspace` to show current capacity or check an export destination before a large operation. `DiskSpaceSnapshot` captures the filesystem containing a file or directory, rather than measuring the file itself.

## Platform availability

macOS, Linux (glibc `statvfs`) and Windows (`GetDiskFreeSpaceExW`). Gate portable code on `DiskSpaceSnapshotSupported()`. `DiskSpaceSupported()` keeps its original macOS-only meaning and returns False on Linux and Windows. On Windows, `TotalBytes` is the caller's quota-scoped total and `FreeBytes` is volume-wide free space, and node counts are not reported (`HasNodeCounts` False, nodes -1).

## Package use

Install `diskspace-0.1.0.buipkg` in the consumer project's `packages/` directory, then use `Import diskspace`. This package is independently sealed; it does not require importing the other desktop integration packages.

## Behavior and lifetime

Missing or inaccessible paths raise `InvalidArgumentException`; on Linux and Windows its `ErrorCode` is the errno or Win32 error. Snapshot byte or node counts above 2^63 - 1 throw `OverflowException`; use `ReadDiskSpaceDetails` for unsigned values. Capacity is a point-in-time observation, not a reservation or a promise that a later write will succeed. Byte and node values can change immediately after the read. The immutable snapshot holds copied integers and needs no close operation.
## Continue reading

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
