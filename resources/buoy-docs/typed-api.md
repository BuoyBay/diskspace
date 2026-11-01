---
title: "diskspace typed API"
summary: "Source-derived public signatures, properties, parameters, defaults, and return values."
category: "Reference"
order: 21
types: ["DiskSpaceDetails", "DiskSpaceLinuxStatvfs", "DiskSpaceSnapshot"]
dependencies: []
symbols: ["DiskSpaceDetails", "DiskSpaceLinuxStatvfs", "DiskSpaceSnapshot"]
related: ["api.md", "getting-started.md", "index.md"]
---

# diskspace typed API

Generated from the package source declarations. Platform guards are shown where present; inherited members are documented on the base type. See the [API contracts](api.md) for behavior and limitations.

## DiskSpace functions

### DiskSpaceSupported

`Public Function DiskSpaceSupported() As Boolean`

Frozen: True only where the original macOS NSFileManager backend runs. Portable code gates on DiskSpaceSnapshotSupported instead.

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### DiskSpaceSnapshotSupported

`Public Function DiskSpaceSnapshotSupported() As Boolean`

True when this compile target has a backend for DiskSpaceSnapshot and ReadDiskSpaceDetails (macOS, Linux statvfs, Windows GetDiskFreeSpaceExW). Compile-time only: it never touches a filesystem or prompts.

No parameters.

#### Returns

| Type |
| --- |
| `Boolean` |

### ReadDiskSpaceDetails

`Public Function ReadDiskSpaceDetails(file As FileSystemItem) As DiskSpaceDetails`

Reads unsigned capacity details for the volume containing file, following symbolic links. Read-only; never prompts. InvalidArgumentException for a Nil/blank/NUL path or a path the platform cannot measure (ErrorCode = errno or Win32 error); UnsupportedOperationException on targets without a backend. macOS also throws InvalidArgumentException when the volume reports no caller-available capacity.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `file` | `FileSystemItem` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `DiskSpaceDetails` |

## DiskSpaceDetails

`Public Class DiskSpaceDetails`

Unsigned capacity details for the volume containing a path. Values are not clipped to the signed range. Field scopes differ by platform; see each field.

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `TotalBytes` | `UInt64` | Yes | No | — | Linux f_blocks*f_frsize and macOS NSFileSystemSize: volume capacity. Windows TotalNumberOfBytes: capacity visible to the caller, which a per-user quota can reduce below volume size (IsTotalCallerScoped). |
| `FreeBytes` | `UInt64` | Yes | No | — | Volume-wide free bytes, including space reserved for root or outside the caller's quota (Linux f_bfree*f_frsize, macOS NSFileSystemFreeSize, Windows TotalNumberOfFreeBytes). On Windows it can exceed TotalBytes. |
| `AvailableBytes` | `UInt64` | Yes | No | — | Bytes the calling user can write (Linux f_bavail*f_frsize, macOS NSURLVolumeAvailableCapacityKey, Windows FreeBytesAvailableToCaller). |
| `IsTotalCallerScoped` | `Boolean` | Yes | No | — | True on Windows, where TotalBytes reports the caller's quota-scoped total rather than volume capacity. False on macOS and Linux. |
| `HasNodeCounts` | `Boolean` | Yes | No | — | True when TotalNodes and FreeNodes hold values. False on Windows and on Linux filesystems that report zero inodes (for example btrfs). |
| `TotalNodes` | `Optional Of UInt64` | Yes | No | — | — |
| `FreeNodes` | `Optional Of UInt64` | Yes | No | — | — |
| `AvailableNodes` | `Optional Of UInt64` | Yes | No | — | Linux f_favail (inodes available to unprivileged users). None elsewhere. |

## DiskSpaceSnapshot

`Public Class DiskSpaceSnapshot`

### Properties

| Name | Type | Read-Only | Shared | Default | Description |
| --- | --- | --- | --- | --- | --- |
| `TotalBytes` | `Integer` | Yes | No | — | — |
| `FreeBytes` | `Integer` | Yes | No | — | — |
| `TotalNodes` | `Integer` | Yes | No | — | -1 when HasNodeCounts is False. |
| `FreeNodes` | `Integer` | Yes | No | — | -1 when HasNodeCounts is False. |
| `AvailableBytes` | `Integer` | Yes | No | — | Bytes usable by the caller (see DiskSpaceDetails.AvailableBytes). macOS reports -1 if the volume does not provide it; the constructor does not throw for that, preserving existing macOS callers. |
| `HasNodeCounts` | `Boolean` | Yes | No | — | False on Windows and inode-less Linux filesystems (nodes are -1). |

### Constructor

`Public Sub Constructor(file As FileSystemItem)`

Signed snapshot. On Linux and Windows, values above 2^63 - 1 throw OverflowException; ReadDiskSpaceDetails returns the unsigned values. TotalBytes/FreeBytes keep each platform's scope (see DiskSpaceDetails).

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `file` | `FileSystemItem` | `ByVal` | Required |

Returns: no value.

## DiskSpaceByteCounts functions

### DiskSpaceCheckedProduct

`Public Function DiskSpaceCheckedProduct(count As UInt64, unit As UInt64, label As String) As UInt64`

count * unit as UInt64; OverflowException when the product exceeds 2^64 - 1.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `count` | `UInt64` | `ByVal` | Required |
| `unit` | `UInt64` | `ByVal` | Required |
| `label` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `UInt64` |

### DiskSpaceSignedCount

`Public Function DiskSpaceSignedCount(value As UInt64, label As String) As Integer`

A UInt64 byte or node count as a signed Integer; OverflowException above 2^63 - 1 instead of a wrapped negative value.

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `value` | `UInt64` | `ByVal` | Required |
| `label` | `String` | `ByVal` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

## DiskSpaceLinuxStatvfs

`Sequential Structure DiskSpaceLinuxStatvfs`

### Fields

| Name | Type | Value / Default | Description |
| --- | --- | --- | --- |
| `BlockSize` | `UInt64` | — | — |
| `FragmentSize` | `UInt64` | — | — |
| `Blocks` | `UInt64` | — | — |
| `FreeBlocks` | `UInt64` | — | — |
| `AvailableBlocks` | `UInt64` | — | — |
| `Files` | `UInt64` | — | — |
| `FreeFiles` | `UInt64` | — | — |
| `AvailableFiles` | `UInt64` | — | — |
| `FileSystemId` | `UInt64` | — | — |
| `Flags` | `UInt64` | — | — |
| `NameMaximum` | `UInt64` | — | — |
| `Reserved0` | `UInt32` | — | — |
| `Reserved1` | `UInt32` | — | — |
| `Reserved2` | `UInt32` | — | — |
| `Reserved3` | `UInt32` | — | — |
| `Reserved4` | `UInt32` | — | — |
| `Reserved5` | `UInt32` | — | — |

## DiskSpaceLinuxBackend functions

### DiskSpaceLinuxQuery

`Public Function DiskSpaceLinuxQuery(filePath As String, ByRef info As DiskSpaceLinuxStatvfs) As Integer`

statvfs on an existing path, following symbolic links. Returns 0 or errno.

Availability: `#If TargetLinux Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `filePath` | `String` | `ByVal` | Required |
| `info` | `DiskSpaceLinuxStatvfs` | `ByRef` | Required |

#### Returns

| Type |
| --- |
| `Integer` |

## DiskSpaceWindowsBackend functions

### DiskSpaceWindowsQuery

`Public Function DiskSpaceWindowsQuery(directoryPath As String, ByRef available As UInt64, ByRef total As UInt64, ByRef free As UInt64) As Integer`

GetDiskFreeSpaceExW for a directory. available = FreeBytesAvailableToCaller (quota-aware), total = TotalNumberOfBytes (the caller's quota-scoped total), free = TotalNumberOfFreeBytes (volume-wide). Returns 0 or the Win32 error.

Availability: `#If TargetWindows Then`

#### Parameters

| Name | Type | Passing | Default |
| --- | --- | --- | --- |
| `directoryPath` | `String` | `ByVal` | Required |
| `available` | `UInt64` | `ByRef` | Required |
| `total` | `UInt64` | `ByRef` | Required |
| `free` | `UInt64` | `ByRef` | Required |

#### Returns

| Type |
| --- |
| `Integer` |
