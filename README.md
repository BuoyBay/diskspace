# Disk capacity and free space

`Import diskspace`. `New DiskSpaceSnapshot(file)` returns read-only signed `TotalBytes`, `FreeBytes`, `AvailableBytes`, `TotalNodes` and `FreeNodes` for the filesystem containing an existing path, plus `HasNodeCounts`. `ReadDiskSpaceDetails(file)` returns the same capacity as unsigned `UInt64` values with explicit scopes. Values are OS snapshots, not reservations or promises that a future write will succeed. Nothing is written and nothing prompts.

On Windows a per-user quota can make `TotalBytes` smaller than the volume and `FreeBytes` larger than `TotalBytes`; the two are never mixed into one number. A file path is measured through its directory.

## Install

From a Buoy consumer project with BPM installed:

```sh
bpm add diskspace --git https://github.com/BuoyBay/diskspace.git --rev refs/heads/main
```

BPM records the resolved commit in its lockfile. Keep that lockfile with the
consumer project for reproducible installs.

## Example

```basic
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

## Build from source

With the native `build-packages` driver from [devtools](https://github.com/BuoyBay/devtools)
on your `PATH`, run from this checkout:

```sh
build-packages --root "$PWD" --compile-consumer diskspace
```

The driver checks source targets and compiles an import consumer. See the
[API guide](docs/API.md) for behavior, limits and any host prerequisites.

## Local Buoy setup

On your own machine, install Buoy and run:

```sh
buoy ai init --both
```


## Contributing

[Contributing](CONTRIBUTING.md) · [License](LICENSE)
