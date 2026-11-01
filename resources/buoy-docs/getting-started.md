---
title: "Disk space getting started"
summary: "Install and use diskspace with an availability check and a complete example."
category: "Guides"
order: 10
keywords: ["diskspace", "install", "example"]
related: ["index.md", "api.md", "typed-api.md"]
---

# Getting started

## Install and import

Maintainers run the private validation suite before release. See the repository README for public build instructions.

## First use

Pass a non-Nil `FileSystemItem` for an existing accessible path. Read the integer counters from the snapshot; `AvailableBytes` is what the current user can write, `FreeBytes` is volume-wide. Construct a new snapshot whenever a fresh measurement is needed.

```buoy
Import diskspace

Sub Main()
    If Not DiskSpaceSnapshotSupported() Then
        Print "Disk-space snapshots are not available on this target."
        Return
    End If
    Dim capacity As New DiskSpaceSnapshot(New FileSystemItem("."))
    Print capacity.AvailableBytes
    Print capacity.TotalBytes
    Dim details As DiskSpaceDetails = ReadDiskSpaceDetails(New FileSystemItem("."))
    If details.IsTotalCallerScoped Then Print "TotalBytes follows this user's quota"
End Sub
```

The example checks for a backend on this compile target first. It does not catch input or access failures; catch exceptions at your application boundary and present a useful message to the user.

## Platform and error handling

macOS, Linux (glibc `statvfs`) and Windows (`GetDiskFreeSpaceExW`). Gate portable code on `DiskSpaceSnapshotSupported()`. `DiskSpaceSupported()` keeps its original macOS-only meaning and returns False on Linux and Windows. On Windows, `TotalBytes` is the caller's quota-scoped total and `FreeBytes` is volume-wide free space, and node counts are not reported (`HasNodeCounts` False, nodes -1). Missing or inaccessible paths raise `InvalidArgumentException`; on Linux and Windows its `ErrorCode` is the errno or Win32 error. Snapshot byte or node counts above 2^63 - 1 throw `OverflowException`; use `ReadDiskSpaceDetails` for unsigned values. Capacity is a point-in-time observation, not a reservation or a promise that a later write will succeed. Byte and node values can change immediately after the read. The immutable snapshot holds copied integers and needs no close operation.
## Continue reading

- [Overview](index.md)
- [Getting started](getting-started.md)
- [API reference](api.md)
