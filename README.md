# From Zero to Docker in under 30 min

#### Step 1, we need a virtualization engine
*there are others hypervisors, but for this 30 min jump into the future, we're going to go with VirtualBox*

[Download VirtualBox](https://download.virtualbox.org/virtualbox/5.2.18/VirtualBox-5.2.18-124319-Win.exe)

#### Step 2, now we need an OS
 * Option A
    - Download the pre-built appliance (this tutorial targets Option A)
 * 	Option B
    - Download your favorite flavor of linux - Option B isn't supported as part of this tutorial

##### Option A
 download the CentOS 7 Appliance [Here](http://someurl)

 1. Start VirtualBox
 2.  Select: File / Import Appliance   
 ![Import Appliance](step2.png)

 3. Locate the File, zerotodocker and select [Next]  
![Locate appliance](step3.png)

 4. &#x1F53B; *Important Step:  On the Appliance Settings, ensure you check “[x] Reinitialize the MAAC Address of the network cards”*  
NOTE: this ensure all traffic is routed to your VM’s mac address and there is not a conflict. With NAT there should be no conflict, however, no one wants to be “that guy”  
![Locate appliance](step4.png)


5. finally import  
![Locate appliance](step5.png)

### Appliance Configuration
1. 
