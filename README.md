# Disk-Health-Research
Detecting the wear on HDD of Earlham servers 

## Topics

- [Research Goal](#research-goal)
- [RAID Levels](#raid-levels)
- [What is SMART](#what-is-smart)
- [What Attributes Are Important](#what-attributes-are-important)
- [Checking SMART parameters](#checking-smart-parameters)
- [Nagios Monitoring System](#nagios-monitoring-system)

### Research Goal

The goal for this research is to predict near-future HDD failure for machines which consists the Earlham College's system. HDD contains crucial data that we cannot afford to get lost, thus we must consistencly ensure the safety of our hard drives. Specifically in this research we are going to utilize SMART tool to check information for every HDDs within each machines. SMART is a system which monitors and tracks HDD's various data and some of the kept variables are significantly helpful for us to predict failures (more information in [What is SMART](#what-is-smart) section). If we see indications of failure, our next step is to change the HDDs of the machine, to keep the currently stored data safe. 

### RAID Levels

RAID is the abbreviation for Redundant Array of Independent Drives. This technologies allows data to be distributed over multiple disks, and furthermore enables data redundancy by making copies of files over different disks. 

* **RAID 0**: Combines two hard disks to create larger storage space for data 
* **RAID 1**: Stacks disks to make copies of files, thus even if one fails it ensures the safety of data 
* **RAID 10**: RAID 0 and RAID 1 combined, meaning larger storage and redundant data

### What is SMART?

Smart, acronym for **Self-Monitoring Analysis and Reporting Technology**, is a inbuilt system of HDD(Hard Disk Drive) / SSD(Solid State Drive) which monitors the internal information of the hard disk itself. These information can be used to detect the wear and age of hard drives to predict HDD / SSD failures. SMART keeps track of various variables and attributes of the hard disk, for example, how long it has been turned on for, and how many times it has been turned on and off. Depending on which quantities we look at, we can come up with an informed guess of which hard disks might be on the verge of wearing out. 

> Interesting thing to note: Whenever they say HDD with 512GB or 1TB of storage, the actual amount of storage the users can use is a little less than prescribed, because some storage is used to store its own information.


### What attributes are important?

1. **Reallocated Sector Count**: Reallocation of data refers to the copying of data to another sector, when the current sector shows any sign of failure. This count refers to how many times remapping has happened in the hard drive. If the number increases this suggests wear of sectors, which ultimately means that the HDD/SSD is wearing out. 

2. **Reported Uncorrectable Errors**: This keeps count of errors within the hard drive which could not be recovered, and it is also an important factor to check when we are predicting the failure of them. 

3. **Disk Temperature**(Temperature_Celsius): Also an important attribute to look as if the disk temperature is over 60 degreed celsius, it has a risk of reducing its lifespan. 


### Checking SMART parameters

#### HDD structure in Hopper (DNS: hopper.cluster.earlham.edu)

<img width="734" alt="Screen Shot 2022-09-15 at 12 45 42" src="https://user-images.githubusercontent.com/113309314/190461098-f63700a3-fd0d-445d-83cd-1154c359196e.png">

I performed ssh from my local machine to a machine called hopper, which is one of our cluster machines. Once remote access is completed, `sudo su - root` was ran in the command prompt to become the root of the machine. Linux has a very strong principle of treating everything as a file, thus these listed HDDs are also treated as a file, which is located in the directory `/dev/`.

#### SMART Parameters 

<img width="981" alt="Screen Shot 2022-09-15 at 12 47 47" src="https://user-images.githubusercontent.com/113309314/190461485-cc98a8d6-dc5a-4b1c-8f4d-c0cc4320a13a.png">

The command `smartctl -a /dev/sda` uses SMART tool to list out all the information about the `sda` hard disk. This example above is the actual data received when running this command in hopper. Out of all the listed attributes, there are some variables that matters more when coming to predicting failures of HDDs.

### Nagios Monitoring System

When checking these SMART data, it requires us to SSH in to the remote host. As our system consists of 40 nodes, it is inpracticle to monitor disk health in such way. Instead, we can use the **Nagios Monitoring System** to make our lives much easier. Monitoring systems helps us to overlook the current situation of the infrastructure. It gives us detail of a lot of things, including SMART. 

#### Nagios Plugins 

Nagios Plugins are extensions often times scripts that are downloaded on the hosts to be able to monitor data regarding the hosts. They process command line arguments, perform a specific check, and return the values to Nagios Core. One of the package deals with SMART. This is often named `check_smart.pl` and is located in the directory `/etc/nagios/plugins`. This script runs the SMART checks, and returns the value up to the Nagios Core for us to see. The main configuration of nagios might be named differently, depending on different operating systems. For CentOS, which is one of the Linux distributions used with our servers, the config file is named `nrpe.cfg`, and it looks something like this: 

```
############################
#  Earlham NRPE Config File
############################

# SETTINGS
log_facility=daemon
debug=0
pid_file=/usr/local/nagios/var/nrpe.pid
server_port=5666
nrpe_user=nagios
nrpe_group=nagios
allowed_hosts=127.0.0.1,::1,159.28.23.250,159.28.23.50
dont_blame_nrpe=1
allow_bash_command_substitution=0
command_timeout=60
connection_timeout=300

# COMMANDS
command[check_load]=/etc/nagios/plugins/check_load -r -w .90,.90,.90 -c .95,.95,.95
command[check_zombie_procs]=/etc/nagios/plugins/check_procs -w 5 -c 10 -s Z
command[check_mem]=/etc/nagios/plugins/check_mem -f -w 20 -c 10 -C
command[check_disk]=/etc/nagios/plugins/check_disk -w 20% -c 10% -u GB -p /
command[check_users]=/etc/nagios/plugins/check_users -w 15 -c 20
command[check_total_procs]=/etc/nagios/plugins/check_procs -w 800 -c 1000
command[check_disk_lovelace]=/etc/nagios/plugins/check_disk -w 20% -c 10% -u GB -p /mounts/lovelace
command[check_service]=/etc/nagios/plugins/check_service.sh -o linux -s $ARG1$

command[check_smart_sda]=/etc/nagios/plugins/check_smart.pl -d /dev/sda -i auto
command[check_smart_sdb]=/etc/nagios/plugins/check_smart.pl -d /dev/sdb -i auto
command[check_smart_sdc]=/etc/nagios/plugins/check_smart.pl -d /dev/sdc -i auto
command[check_smart_sdd]=/etc/nagios/plugins/check_smart.pl -d /dev/sdd -i auto

# INCLUDES - varies by machine
#include=/etc/nagios/nrpe_local.cfg
#include_dir=/etc/nagios/nrpe.d/
```

We can check that the command lines for running the `check_smart.pl` is being processed in the main configuration file.


#### Removing Airflow_Temperature_Cel

One problem we faced during this research was that Nagios Core was showing WARNING signs for some hosts even when they were just fine. One of the reason this occured is because Nagios keeps record of old data too. If one of the SMART attributes resulted in a problematic value in the past; meaning when it has given out WARNING signd in past, then it just stays there until the system administrators do something about it. This happened for us with several servers. Taking one of our cluster servers, `sakurai`, as an example:

<img width="1199" alt="Screen Shot 2022-11-13 at 20 12 46" src="https://user-images.githubusercontent.com/113309314/201555716-be9b3337-3500-4bd5-b8f8-9f975a86f77c.png">

Notice here that the error duration has been more than 6 days. If we look at the error more in detail, we noticed that the disk temperature had exceeded over 50 degres Celsius for a moment in the past, but was now back to normal. Furthermore if we ran `root@sakurai:~# /etc/nagios/plugins/check_smart.pl -d /dev/sdc -i auto` on the command line this was the output: 

```
WARNING: Drive  ST380215AS S/N 9QZCBF4J:  Attribute Airflow_Temperature_Cel failed at In_the_past, |Raw_Read_Error_Rate=0 Spin_Up_Time=0 Start_Stop_Count=150 Reallocated_Sector_Ct=0 Seek_Error_Rate=126393501 Power_On_Hours=97307 Spin_Retry_Count=0 Power_Cycle_Count=150 Reported_Uncorrect=0 High_Fly_Writes=0 Airflow_Temperature_Cel=28 Temperature_Celsius=28 Hardware_ECC_Recovered=242332071 Current_Pending_Sector=0 Offline_Uncorrectable=0 UDMA_CRC_Error_Count=0 Multi_Zone_Error_Rate=0 Data_Address_Mark_Errs=0
```

If we ran the same command but with addtional option `-e 190`, which means "exclude SMART ID#190 (Temperature)": `root@sakurai:~# /etc/nagios/plugins/check_smart.pl -d /dev/sdc -i auto -e 190`, we get the following output.

```
OK: Drive  ST380215AS S/N 9QZCBF4J: no SMART errors detected. |Raw_Read_Error_Rate=0 Spin_Up_Time=0 Start_Stop_Count=150 Reallocated_Sector_Ct=0 Seek_Error_Rate=126393534 Power_On_Hours=97307 Spin_Retry_Count=0 Power_Cycle_Count=150 Reported_Uncorrect=0 High_Fly_Writes=0 Airflow_Temperature_Cel=28 Temperature_Celsius=28 Hardware_ECC_Recovered=242332083 Current_Pending_Sector=0 Offline_Uncorrectable=0 UDMA_CRC_Error_Count=0 Multi_Zone_Error_Rate=0 Data_Address_Mark_Errs=0
```

This change was only a temporary on terminal output however I wanted to permanently change this, so that we don't get thrown off by the WARNING signs on Nagios even if the hosts were completely fine. I made this change to the main configuration file located in `/etc/nagios/` of each hosts. For every command lines for each disks I added `-e 190` so that the plugins don't perform the check for disk temperature.

```
command[check_smart_sda]=/etc/nagios/plugins/check_smart.pl -d /dev/sda -i auto -e 190
command[check_smart_sdb]=/etc/nagios/plugins/check_smart.pl -d /dev/sdb -i auto -e 190
command[check_smart_sdc]=/etc/nagios/plugins/check_smart.pl -d /dev/sdc -i auto -e 190
command[check_smart_sdd]=/etc/nagios/plugins/check_smart.pl -d /dev/sdd -i auto -e 190
```

The outcome for this change is demonstrated in the screenshot attached below. 

<img width="1045" alt="Screen Shot 2022-12-17 at 17 32 37" src="https://user-images.githubusercontent.com/113309314/208271435-604b3099-ab31-4890-8b95-ed92c5a03e27.png">

Now the WARNING sign has been taken care of. 

