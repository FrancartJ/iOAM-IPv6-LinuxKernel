# iOAM-IPv6-LinuxKernel
Implementation of the iOAM pre-allocated trace option [https://tools.ietf.org/html/draft-ietf-ippm-ioam-data-02] in the Linux kernel.

The file ioam.patch is the patch which must be applied on the Linux kernel.

The file test.c is a simple c program using the ioctl to create the Hop-By-Hop options header containing the iOAM pre-allocated trace which will be inserted in the packet.

# Using the patch:
To use the patch, one must download the version 4.12 of the linux kernel. Then apply the patch and build the kernel. To build it, one just need to use the following command. The -j5 is to speed up the process, the number must be equal to the number of cpu core + 1 so in this case there are 4 cpu cores.
```
make ARCH=x86 bzImage -j5
make ARCH=x86 modules -j5
``` 

# Using the implementation:

There are three types of node which used iOAM
- The first will insert the iOAM trace in the packet and add the information
- The second will add the information
- The last will add the information and remove the trace

To make the node capable to insert the iOAM trace, you can use the program test.c. This program takes several argument as input:
- m : which is the mode. For the moment you must only use the mode 1 which will create a pre-allocate ioam trace or mode 0 which will free the trace.
- f : the frequency at which the iOAM trace will be inserted. For example, 0 and 1 will make the interface inserts in every packet the iOAM trace, 2 will make it inserts in 1 packet over 2, 3 will make it inserts in 1 packet over 3 and so on.
- h : the number of node in the node data list of the iOAM trace.
- o : the iOAM trace type in decimal.
- i : the interface which will insert the iOAM trace in the packet if it is the ingress interface of the packet.

Here is an example:
```
./test -m 1 -f 1 -h 3 -o 32768 -i ens3
``` 
In this case, the interface ens3, will insert an iOAM trace in every packet going through it. This trace will collect the hop limit and node identifer data on 3 hops.

To remove the iOAM trace of a packet and so retreive the information, one must use the following command:
```
sudo sysctl -w net.ipv6.conf.ifname.ioam6_if_decap=1
``` 
Where ifname is the name of the ingress interface.
To see the information, one can use the following command:
```
tail -f /var/log/syslog
``` 
He will then see something like:

There are differents information which can be collected by the iOAM trace [https://tools.ietf.org/html/draft-ietf-ippm-ioam-data-02]. Not all the possible data can be collected for the moment but the following one can:
- hop limit and node identifier in normal and wide format.
- ingress and egress interface identifier in normal and wide format.
- timestamp in second and in nanosecond.
- application data in normal an wide format.

Some of them can be changed by a application or by the system administrator. Below there is a list of command which can be used to change those values.
- To change the node identifier one can use the following command where V is the value wanted. If this value is greater than 2^(14)-1, the change will not be applied.
```
sudo sysctl -w net.ipv6.ioam6_node_id=V
``` 
- To change the interface identifier, one can use the following command where V is the value wanted and ifname the name of the interface.
```
sudo sysctl -w net.ipv6.conf.ifname.ioam6_if_id=V
``` 
- To change the application data value, one can use the following command where V is the value wanted.
```
sudo sysctl -w net.ipv6.app_data=V
``` 
- To change the second part of node identifier (contd) in its wide format, one can use the following command where V is the value wanted.
```
sudo sysctl -w net.ipv6.ioam6_node_id_cond=V
``` 
- To change the second part of application data value (contd) in its wide format, one can use the following command where V is the value wanted.
```
sudo sysctl -w net.ipv6.app_data_cond=V
``` 
