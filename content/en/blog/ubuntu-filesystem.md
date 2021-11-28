+++
title = "Ubuntu Filesystem"
banner = "https://sorasystem.sirv.com/photos/magic.jpg"
date = 2016-11-12
# description: "The ASEAN's Next Great Idea contest looks for the Top 20 ideas in Southeast Asia. We're very thankful to have made it to the list!"
# linkb: "news/hanoi-techest-2016"
# linkbtext: "hanoi-techest"
# linkf: "news/startup-weekend-cambodia"
# linkftext: "startup-weekend"
draft= true
+++


## /lost+found

This stores recovered files that may have been lost during sudden shutdowns. 

When Ubuntu shuts down unexpectedly, it does a lengthy filesystem check* using fsck. It will try to recover any corrupt files that it finds and places it in this directory.

> *Ext3, a journalled filesystem, will finish  the check faster than ext2 


<!-- . Fsck will go through the system and try to . The result of this recovery operation will be placed in this directory. The files recovered are not likely to be complete or make much sense but there always is a chance that something worthwhile is recovered. 
 -->

Each partition has its own lost+found directory. If you find files in there, try to move them back to their original location. 

<!-- If you find something like a broken symbolic link to 'file', you have to reinstall the file/s from the corresponding RPM, since your file system got damaged so badly that the files were mutilated beyond recognition. 
 -->
<!-- Below is an example of a /lost+found directory. As you can see, the vast majority of files contained here are in actual fact sockets. As for the rest of the other files they were found to be damaged system files and personal files. These files were not able to be recovered. -->

## /home

Each user has its own home directory under '/home/$USER' (~/).

Personl config files here have the convention: .name and are hidden. These can be ssen by unhiding in the file manager or `ls -a` in the terminal.

If there is a conflict between personal and system wide configuration files, the settings in the personal file will prevail.

<!-- Dotfiles most likely to be altered by the end user are probably your .xsession and .bashrc files. The configuration files for X and Bash respectively. They allow you to be able to change the window manager to be startup upon login and also aliases, user-specified commands and environment variables respectively. Almost always when a user is created their dotfiles will be taken from the /etc/skel directory where system administrators place a sample file that user's can modify to their hearts content.

/home can get quite large and can be used for storing downloads, compiling, installing and running programs, your mail, your collection of image or sound files etc.
 -->

## /media

Amid much controversy and consternation on the part of system and network administrators a directory containing mount points for removable media has now been created. Funnily enough, it has been named /media.

This directory contains subdirectories which are used as mount points for
removeable media such as floppy disks, cdroms and zip disks.

