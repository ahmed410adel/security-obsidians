![[Pasted image 20260206213847.png]]

## Applications
applications
## Application Framework
control applications activity
## Android Runtime
Isolation purpose between applications (Dalvik Virtual Machine)
##  Libraries
work as interface between applications and kernel 
## Linux Kernel
allow the OS to communicate with the hardware using drivers

![[Pasted image 20260206214755.png]]

APK is a package that contains a lot of files
1. Libraries `files.so`
2. `AndroidManifest.xml` contain the configurations of APK like permissions
3. resources
4. META-INF directory contains signing information's
	hash of every file above to ensure integrity
5. `Classes.dex` code file



#### Android Application components
The behavior and interaction between these components is defined in the manifest.xml file in the application.
- Activities: They dictate the UI and handle the user interaction to the  smartphone screen
- Services:  They handle background processing associated with an application.
- Broadcast Receivers: They handle communication between Android OS and applications.
- Content Providers: They handle data and database management issues.

## Android security layer
1. DAC discretionary access control
2. MAC mandatory access control

# Notes :
`.apk` is the extension of the android application