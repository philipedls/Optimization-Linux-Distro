# Optimization Linux Distro by Philipe Luna
this sequence of steps should work on any linux distributions based on debian or ubuntu

## Tested distributions: Ubuntu, Linux Mint, Xubuntu, Kubuntu, KDE Neon and Elementary OS

### 1: Clearing RAM cache

Sometimes the system allocates too much memory and fails to organize the space that was previously used, causing your memory to be all loaded with information that is no longer being used by the system at that time. Learn how to free up unused memory on Linux.

#### GNU / Linux provides a way to clear the RAM cache

Every Linux system has three options for clearing the cache without interrupting any processes or services.

* Clear the cache only:

`# sync; echo 1 > /proc/sys/vm/drop_caches` 

* Clean dentries and inodes:

`# sync; echo 2 > /proc/sys/vm/drop_caches` 

* Clear cache, dentries and inodes:

`# sync; echo 3 > /proc/sys/vm/drop_caches` 

Add permission to the file:

`chmod 777 /path_to_file/namefile.sh` 
Or
`chmod +x /path_to_file/namefile.sh` 

Understanding all the commands above and especially the risk of using the third command, I created the following ShellScript file:

``` sh
#!/bin/bash
sync; echo 3 > /proc/sys/vm/drop_caches 
``` 

And this script contained in the file can be executed periodically using crontab.
After creating the file, for example "filename.sh", open the terminal and run the following commands:

`crontab -e` 

Choose your preferred text editor. In this tutorial, nano is used.
And then you will have a terminal view similar to this:

```sh
# Edit this file to introduce tasks to be run by cron.

# 

# Each task to run has to be defined through a single line

# indicating with different fields when the task will be run

# and what command to run for the task

# 

# To define the time you can provide concrete values for

# minute (m), hour (h), day of month (dom), month (mon), 

# and day of week (dow) or use '*' in these fields (for 'any').

# 

# Notice that tasks will be started based on the cron's system

# daemon's notion of time and timezones.
# 

# Output of the crontab jobs (including errors) is sent through

# email to the user the crontab file belongs to (unless redirected).
# 

# For example, you can run a backup of all your user accounts

# at 5 a.m every week with:

# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/

# 

# For more information see the manual pages of crontab(5) and cron(8)

# 

# m h  dom mon dow   command 
```

Now add your configured script to the bottom line of crontab

``` sh
# Edit this file to introduce tasks to be run by cron.

# 

# Each task to run has to be defined through a single line

# indicating with different fields when the task will be run

# and what command to run for the task

# 

# To define the time you can provide concrete values for

# minute (m), hour (h), day of month (dom), month (mon), 

# and day of week (dow) or use '*' in these fields (for 'any').

# 

# Notice that tasks will be started based on the cron's system

# daemon's notion of time and timezones.
# 

# Output of the crontab jobs (including errors) is sent through

# email to the user the crontab file belongs to (unless redirected).
# 

# For example, you can run a backup of all your user accounts

# at 5 a.m every week with:

# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/

# 

# For more information see the manual pages of crontab(5) and cron(8)

# 

# m h  dom mon dow   command 

#  # Edit this file to introduce tasks to be run by cron.
# 

# Each task to run has to be defined through a single line

# indicating with different fields when the task will be run

# and what command to run for the task

# 

# To define the time you can provide concrete values for

# minute (m), hour (h), day of month (dom), month (mon), 

# and day of week (dow) or use '*' in these fields (for 'any').

# 

# Notice that tasks will be started based on the cron's system

# daemon's notion of time and timezones.
# 

# Output of the crontab jobs (including errors) is sent through

# email to the user the crontab file belongs to (unless redirected).
# 

# For example, you can run a backup of all your user accounts

# at 5 a.m every week with:

# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/

# 

# For more information see the manual pages of crontab(5) and cron(8)

# 

# m h  dom mon dow   commandbs (including errors) is sent through

# email to the user the crontab file belongs to (unless redirected).
# 

# For example, you can run a backup of all your user accounts

# at 5 a.m every week with:

# 0 5 * * 1 tar -zcf /var/backups/home.tgz /home/

# 

# For more information see the manual pages of crontab(5) and cron(8)

# 

# m h  dom mon dow   command

# * * * * * root /usr/bin/sh /home_to_file/namefile.sh

```

All ready. Now your script is once every minute. If you want to change the frequency, consult the crontab [documentation](https://crontab.guru/)

#### If you have problems configuring or running crontab use this recommendation

Open the terminal and use your favorite text editor (I'll use nano as an example again)

`nano ~/clean_cache.sh` 

And inside the file copy and paste the following command

``` sh
#!/bin/bash
while [ 1 ];
do
 clear
 free -h && sync && echo 3 > /proc/sys/vm/drop_caches && free -h
 sleep 70
done 
```

Sleep is associated with seconds, meaning that this algorithm is configured for the script to run every 70 seconds.

Finally save the file press Crl + O.

To leave the environment of the nano editor press Crl + X

To run the script use this command:

`/.clean_cache.sh` or `sudo /.clean_cache.sh` 

### 2: Improving Swap Management

#### Changing the swappiness amount

Swapiness is a value from 0 to 100 that controls the degree to which the system changes. A high value gives priority to system performance, aggressively changing processes out of physical memory when they are not active. 

A low value gives priority to the interaction and avoids alteration processes outside physical memory as long as possible, which decreases the response latency. The default value is 60.
In this case you need to change the /etc/sysctl.conf file, so that the change is permanent.

My swapiness is configured as follows:

* Open the file with your favorite text editor (I'll use the nano):

`sudo nano /etc/sysctl.conf` 

* Now, copy and paste the following code at the end of the file::

``` sh
# Reduces Cache usage

vm.swappiness=10

# Improves Cache management

vm.vfs_cache_pressure=50
```

Then save and exit the editor.
Restart your machine.

#### Using zRAM

The zram, formerly called compcache, is a module of the Linux kernel to create a block device compressed in RAM, that is, a RAM disk, but with a dynamic "disk" compression. The block device created with zram can be used for swapping or as a general purpose RAM disk. The two most common uses for zram are for storing temporary files (/ tmp) and as a swap "disk".

Open the terminal and run the command below to install

`sudo apt install zram-config` 

#### Disabling swap (Not recommended)

This step is for those who have a considerable amount of cache and want to disable the use of swap

`sudo swapoff -a` 

### 3: Improving Processor Performance

Depending on your processor and with this method it may even be possible to control the clock and activate or deactivate the boots mode which increases the clock even more

#### Using CPUFreq to increase performance or to increase processor powersave

To install it open the terminal copy it and paste the following command

`sudo apt install cpufrequtils indicator-cpufreq` 

After that just restart the computer or log out the indicator will be on your panel.
The options the program will give varied depending on the capabilities of your processor.

![Alt](https://3.bp.blogspot.com/-5yM6AtRMkpY/UjjJfRXy-NI/AAAAAAAAIdw/ulqvH8GEiOk/s400/Menu_001.png)

### 4: Disable "unnecessary" processes that start with the system

Like any operating system, Kubuntu or any other OS has several applications and services that start with the system.

As the deactivation of programs that are started together with the operating system can vary, so it is recommended that you consult the official websites of your Linux distro to perform this step.
