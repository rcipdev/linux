## CMPE283 Assignments

## University Details:

San Jose State University

Course: CMPE 283 - Virtualization Technology

Professor: Mike Larkin

Team -

1. Ruchik Pravasi (SID: 017452461)

# Assignment 1 Discovering VMX features

Assignment is to create a Linux kernel module that will query various MSRs to determine virtualization features available in your CPU. This module will report (via the system message log) the features it discovers.

Questions

1. For each member in your team, provide 1 paragraph detailing what parts of the lab that member implemented / researched. (You may skip this question if you are doing the lab by yourself).
2. Describe in detail the steps you used to complete the assignment. Consider your reader to be someone skilled in software development but otherwise unfamiliar with the assignment. Good answers to this question will be recipes that someone can follow to reproduce your development steps.

Individual Contributions:

Answers:

1. I did all by myself

2.

Steps followed for Assignment 1:

1.  Create GCP account and use terminal to create image and VM
2.  Copy below command to create image and enable nested virualization

```bash
gcloud compute images create virtualizationnested --source-image=ubuntu-pro-2004-focal-v20231213 --source-image-project=ubuntu-os-pro-cloud --licenses="https://www.googleapis.com/compute/v1/projects/vm-options/global/licenses/enable-vmx"
```

3.  Copy below command to create instance from above created image

```bash
gcloud compute instances create virtualization --zone=us-east4-a --image=virtualizationnested
```

We will be using Ubuntu 20.04.3 in by allocating 8 VCPU's, and 100 GB disk space.

4. Check if VM created is nested virtualizable or not by below command

```bash
cat /proc/cpuinfo
```

This command will show vmx flags, if flags are not shown VM is not nested virtualizable.

4.  Fork the master branch of linux repository from https://github.com/torvalds/linux to your github account.
5.  Install Git using command "sudo apt install git".
6.  Ensure git is installed using "git --version". If version is not displayed, try update command "sudo apt-get update" and reinstall git.
7.  Configure git using following commands

    - git config --global user.name "<your_github_username"
    - git config --global user.email "<your_github_email_address>"

8.  Clone the forked repository into your VM created on GCP using git clone command (Note: Install Git if not installed already).

9.  Steps for cloning

    - Go to github.com and point the repository to linux.
    - Click on Code button that appears and copy the URL for HTTPS.
    - Change the working directory where you want to work with the repo and type below command

    ```bash
    git clone https://github.com/rcipdev/linux.git
    ```

    - Ensure git is pointing to the forked repo and not the torvalds/linux repo.
    - Git downloads the code and resolves dependencies from the link pointed.

10. These are the list of libraries required for compiling and installing kernel code

    - sudo apt-get update
    - sudo apt-get upgrade
    - sudo apt-get install libncurses-dev
    - sudo apt-get install libssl-dev
    - sudo apt-get install make
    - sudo apt-get install gcc
    - sudo apt-get install flex
    - sudo apt-get install bison
    - sudo apt-get install libelf-dev
    - sudo apt-get install libelf-dev
    - sudo apt install zstd

11. Steps for building kernel

    - Navigate to linux folder
    - Type Command "make menuconfig" to view the interactive shell for loading kernel configuration (Nothing to be done/selected here, it displays configuration menu)
    - Exit from menu config

    - Check for current kernel version using command "uname -a"
    - To avoid version conflict/mismatch, copy the current kernel config file to a new config file in current (inside linux folder) directory using below command

    ```bash
    cp /boot/config-5.15.0-1047-gcp /home/rkpravasi77/linux/.config
    ```

    - Type command

    ```bash
    make oldconfig
    ```

    - Type command

    ```bash
    make prepare
    ```

    - There were some certificate-related errors while running the "make prepare" command. I solved it by following the method outlined below.

    ```bash
        scripts/config --disable SYSTEM_TRUSTED_KEYS
        scripts/config --disable SYSTEM_REVOCATION_KEYS
    ```

    - Make modules using

    ```bash
    make -j 8 modules
    ```

    (Replace 8 with the number of vcpu's allocated during VM creation)

    - Type command for making kernel

    ```bash
    make -j 8
    ```

    - Type command for installing modules (Note: INSTALL_MOD_STRIP=1 is used to skip debugging information)

    ```bash
    sudo make INSTALL_MOD_STRIP=1 modules_install
    ```

    - Type command

    ```bash
    sudo make install
    ```

    - Type command to restart the VM inorder to apply our changes

    ```bash
    sudo reboot
    ```

12. After rebooting, use "uname -a" to determine the kernel version. If the kernel version and timestamp have been updated to the most recent version, the kernel is properly installed and running.

13. Create a folder named cmpe283 inside linux folder and add cmpe283-1.c and Make file to this cmpe283 folder.
14. Navigate to cmpe283 folder and type "make" to generate kernel object file for cmpe283-1.c
15. Verify if the object file is created using "ls |grep \*.ko"
16. Next step is to insert the cmpe283-1.ko into kernel. Use below steps to do that
    - sudo insmod cmpe283-1.ko
    - Check if the module is inserted using "lsmod | grep cmpe283". It should display cmpe283_1.
17. Finally, use "dmesg" command to display vmx features.

Output Screenshots

19. Commit cmpe283-1.c and Makefile to the repository.
