---
title: 7-Download-files-ffrom-Baidunetdisk-by-Aria2
date: 2019-09-09 06:13:26
tags:
---

Because of the intellectual property protection laws are so strict in Germany, downloading with p2p tools like baidunetdisk or xunlei online is very dangourous and is likely to be discovered by online police at any time. So we can try to replace these p2p tools with Aria2 that is faster and sampler.

<!--more-->

## Linux:

1. download the aria2 with instruction:
```
sudo apt-get install aria2
```
2. create a configuration file:
aria2.conf
```
#usename
#rpc-user=user
#password
#rpc-passwd=passwd
#the above suthentication method is not recommended. It is recommended to use the following token method
#setup the encrypted key
#rpc-secret=token
#allow rpc
enable-rpc=true
#allow all sources, web interface cross domain permissions required
rpc-allow-origin-all=true
#Allow external access, if false, only listen to local port
rpc-listen-all=true
#RPC port is modified only when the default port is occupied
#rpc-listen-port=6800
#maximum simultaneous downloads(number of tasks), route suggested value: 3
max-concurrent-downloads=5
#resume from break point
continue=true
#number of connections to the server
max-connection-per-server=5
# minimum file shard size, the maximum number of download threads depends on how many slices can be split, which is important for small files.
min-split-size=10M
#Maximum number of threads per file, recommended route: 5
split=10
#
max-overall-download-limit=0
#limitation of download speed 
max-download-limit=0
#limitation of upload speed
max-overall-upload-limit=0
#limitation of uploading a single file
max-upload-limit=0
#Disconnect the connection that is too slow
#lowest-speed-limit=0
#For verification putposes, a release version after 1.16.1 is required
#referer=*
# File save path, default to the current boot location
# !!!! change this to the path in your computer 
dir=/home/yuhao/Downloads 
# File caching, using the built-in file cache, is you don't believe the Linux kernel file cache and disk built-in cache, you need version 1.16 and above
#disk-cache=0
#Another way to cache Linux files, make sure that the kernel you are using supports thsi option before use. It requires 1.15 and above
#enable-mmap=true
#File pre-allocation, which can be effectively reduce file fragmentation and improve disk performance. Th dis advantage is that the pre-allocation time is longer.
# Time required none < falloc ? trunc << prealloc, falloc and trunc require file system and kernel support>
file-allocation=prealloc
```

3. start configuration file:
```
aria2c --conf-path=<path> # <path> is the absolute path of aria2.conf
```
Like:
```
D:\aria2\aria2.conf
```
Adding -D parameter can make the Aria2 run in the background, wven you close the terminal, it will not stop running.

4. Online Aria2 monitor:
Change the RPC PATH in the aria2.conf to http://localhost:6800/jsonrpc

5. Install and enable Baiduexporter Add-ons in Firefox or Chrome(invalid in Chrome?)

6. Open the link of resource in Baidunetdisk, then login in the baidu account.

7. select the export download-aria2c RPC download, and enter the confimation code.

8. The download information will be displayed on the terminal.

Reference:

+ Download Baidunetdisk and 115 resources using Aria2
+ Install and use aria2 to download Baidunetdisk content
