# Introduction 
Microsoft Virtual File System for Perforce (**P4VFS**)

The P4VFS is a Windows service that allows you to sync files from Perforce very quickly using almost no disk space. The contents of a file will be download automatically on-demand when first needed. You can seemlessly mix any use of P4VFS virtual sync with regular Perforce sync's using P4V and p4.exe.

For example, you can use the P4VFS to perform a "virtual sync" to a Perforce filespec and it will complete relatively quickly, and the files will exist on machine as usual. However, the actual size of the files on disk will be zero, and the file contents will be downloaded on-demand during the first read operation.

# Installation
You can install the latest signed release of P4VFS from nuget:
> [P4VFS.Setup](https://xboxstudios.visualstudio.com/The%20Coalition/_artifacts/feed/Packages)

The entire history of release notes is included with the installer.
> [Release Notes](https://xboxstudios.visualstudio.com/The%20Coalition/_git/P4VFS?path=%2Fsource%2FP4VFS.Console%2FP4VFS.Notes.txt)

# Build and Test
#### Build Requirments:
1. Visual Studio 2019 version 16.11.9 or later
1. Windows 10 SDK version 10.0.19041.0
1. Windows 10 WDK version 10 (2004)

#### Instructions:
1. Using Visual Studio 2019, open solution file P4VFS.sln
1. Build solution configurations Release/Debug for regular user mode development including the current production signed driver binaries. Build solution configuration Release-Dev/Debug-Dev for local built, test signed, driver binaries.
1. Run P4VFS.Setup project to install local build.

#### Testing:
1. Full suite of unit tests can be run locally by first opening Visual Studio as Administrator, and building the P4VFS.sln solution
1. In the P4VFS.Console project, set the debug command arguments to:
    ```
    p4vfs.exe test
    ```

# Technical Overview
P4VFS is a Windows service, driver, and console application that allows us to sync files from Perforce immediately, and then actually download the file contents on-demand. It introduces the concept of a "Virtual" sync, where a file revision can be sync'ed from Perforce and will exist locally on disk with a correct size, but zero disk-size, until accessed. When first opened, the Windows NTFS file system will automatically download file's contents, and the file will be read as expected.

The P4VFS is intended to work perfectly seamless with regular p4 and p4v usage. There is no need for special perforce configuration, special clientspecs, or any other non-sense. You can feel free to sync files immediately using **p4.exe**, or sync a zero sized "offline" file with contents on demand using **p4vfs.exe**.

P4VFS is officially supported and tested on Windows 8.1 and Windows 10. If you are running Windows 8.0 or previous, I don't recommend installing it.

# Advanced Usage
The main program that you'll use to do a virtual sync, and possibly other P4VFS operations, is **p4vfs.exe**. 

    C:\Program Files\P4VFS\p4vfs.exe

 The tool has a very similar interface to **p4.exe**. If you want ever need to terminate **p4vfs.exe** while in-progress, simply use Ctrl+C or Ctrl+Break as usual, or just terminate it with the Windows Task Manager. A p4vfs sync will always terminate gracefully and always leave your client consistent with Perforce. Take a look at the command help:

    p4vfs.exe help
    p4vfs.exe help sync
 
Try syncing as usual: 

    p4vfs.exe sync //depot/tools/dev/...
    p4vfs.exe sync //depot/tools/dev/...@600938
    p4vfs.exe sync //depot/tools/dev/...#head  //depot/tools/release/...@599820

The tool respects P4CONFIG file usage, as well as supports typical configuration settings (just like **p4.exe**)
 
    p4vfs.exe -c joe-pc-depot -u contonso\joe -p p4-contoso:1666 sync //depot/tools/dev/...
