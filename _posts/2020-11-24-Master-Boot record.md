---
published: true
---
Master boot recod is one which is responsible for loading the kernel or OS into the RAM.<br/><br/>

The Master Boot Record (MBR) is the information in the first sector of any hard disk or diskette that identifies how and where an operating system is located so that it can be boot (loaded) into the computer's main storage or random access memory. The Master Boot Record is also sometimes called the "partition sector" or the "master partition table" because it includes a table that locates each partition that the hard disk has been formatted into. In addition to this table, the MBR also includes a program that reads the boot sector record of the partition containing the operating system to be booted into RAM. In turn, that record contains a program that loads the rest of the operating system into RAM.<br/>

you can read the MBR using the below command in linux(where the /dev/sd1 might change based on the mount on your machine):<br/>
```java
dd if=/dev/sda1 of=mbr.bin bs=512 count=1
we can read the above binary file using "od -xa mbr.bin"
 ```
