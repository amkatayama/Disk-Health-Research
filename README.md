# Disk-Health-Research
Detecting the wear on HDD of Earlham servers 

## Topics

- [Research Goal](#research-goal)
- [What is SMART](#what-is-smart)
- 

### Research Goal



### What is SMART?

Smart, acronym for **Self-Monitoring Analysis and Reporting Technology**, is a inbuilt system of HDD(Hard Disk Drive) / SSD(Solid State Drive) which monitors the internal information of the hard disk itself. These information can be used to detect the wear and age of hard drives to predict HDD / SSD failures. SMART keeps track of various variables and attributes of the hard disk, for example, how long it has been turned on for, and how many times it has been turned on and off. Depending on which quantities we look at, we can come up with an informed guess of which hard disks might be on the verge of wearing out. 

> Intersting thing to note: Whenever they say HDD with 512GB or 1TB of storage,  the users can use to store data


### What attributes are important to look at?

1. **Reallocated Sector Count**: Reallocation of data refers to the copying of data to another sector, when the current sector shows any sign of failure. This count refers to how many times remapping has happened in the hard drive. If the number increases this suggests wear of sectors, which ultimately means that the HDD/SSD is wearing out. 

2. **Reported Uncorrectable Errors**: This keeps count of errors within the hard drive which could not be recovered, and it is also an important factor to check when we are predicting the failure of them. 

3. **Disk Temperature**(Temperature_Celsius): Also an important attribute to look as it is said that if the disk temperature is over 60 degreed celsius, it has a risk of reducing its lifespan. 


### 
