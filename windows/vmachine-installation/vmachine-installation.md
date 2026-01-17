Steps to install virtual machines.
# Server 1

![[server1-installation-01.png]]
![[server1-installation-02.png]]
![[server1-installation-03.png]]
![[server1-installation-04.png]]
![[server1-installation-05.png]]
![[server1-installation-06.png]]
![[server1-installation-07.png]]
![[server1-installation-08.png]]
![[server1-installation-10.png]]
![[server1-installation-011.png]]
![[server1-installation-11.png]]
# Server 2

> Same steps as server1

# Client

> Same steps as server1 until...

![[client-installation-01.png]]
![[client-installation-02.png]]
![[client-installation-03.png]]
![[client-installation-04.png]]
![[client-installation-05.png]]
![[client-installation-06.png]]
![[client-installation-07.png]]
![[client-installation-08.png]]
![[client-installation-09.png]]
![[client-installation-10.png]]
![[client-installation-11.png]]
![[client-installation-12.png]]
![[client-installation-13.png]]
![[client-installation-14.png]]
![[client-installation-15.png]]
![[client-installation-16.png]]
# After installation

Once the machines are installed, we are going to take off the iso disks from the machines.
This way we are not reinstalling every time we turn on the machines.

Turn off the machines and change order and disable Floppy and Optical devices.

![[server1-installation-after.png]]

```powershell title="snapshot after installations"
VBoxManage snapshot "server1" take "01_installed" --description="This is the virtual machine after the installation."

VBoxManage snapshot "server2" take "01_installed" --description="This is the virtual machine after the installation."

VBoxManage snapshot "client" take "01_installed" --description="This is the virtual machine after the installation."
```