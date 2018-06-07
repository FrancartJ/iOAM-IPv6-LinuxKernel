# iOAM-IPv6-LinuxKernel
Implementation of the iOAM pre-allocated trace option in the Linux kernel.

The file ioam.patch is the patch which must be applied on the Linux kernel.
The file test.c is a simple c program using the ioctl to create the Hop-By-Hop options header containing the iOAM pre-allocated trace which will be inserted in the packet. This program takes several arguments. To have the list of argument, one just needs to execute the program without any argument.
