# Disk-Health-Research
Detecting the wear on HDD of Earlham servers 

## Topics

- [Research Goal](#research-goal)
- [RAID Levels](#raid-levels)
- [What is SMART](#what-is-smart)
- [What Attributes Are Important](#what-attributes-are-important)
- [Checking SMART parameters](#checking-smart-parameters)

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
<img width="864" alt="Screen Shot 2022-09-15 at 11 31 29" src="https://user-images.githubusercontent.com/113309314/190445355-7575124b-a293-49dd-a34a-d233fd9bd92a.png">

I performed ssh from my local machine to a machine called hopper, which is one of our cluster machines. Once remote access is completed, `sudo su - root` was ran in the command prompt to become the root of the machine. Linux has a very strong principle of treating everything as a file, thus these listed HDDs are also treated as a file, which is the directory `/dev/`.




