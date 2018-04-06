## linux

参考http://linux-hacking-guide.blogspot.com/2015/05/convert-vmware-virtual-machine-to-kvm.html

将vmdk和vmx传到kvm主机：
```
qemu-img convert '/home/test/桌面/Metasploitable.vmdk' -f vmdk -O qcow2 '/home/test/桌面/Metasploitable.qcow2'
```
下载vmware2libvirt.tar.gz(https://github.com/t3note/note1/blob/master/file/vmware2libvirt.tar.gz)到kvm主机
```
tar -zxvf vmware2libvirt.tar.gz
cd vmware2libvirt
chmod 777 *
pyhton vmware2libvirt -f /home/test/桌面/Metasploitable.vmx > /home/test/桌面/Metasploitable.xml

cd /home/test/桌面/

ln -s /usr/libexec/qemu-kvm /usr/bin/kvm
virsh -c qemu:///system define Metasploitable.xml
```
修改虚拟机配置
```
virsh edit Metasploitable2-Linux
```
相关部分修改成如下：
```

    <disk type='file' device='disk'>
      <driver name='qemu' type='qcow2'/>
      <source file='你的Metasploitable.qcow2文件的绝对路径'/>
      <target dev='hda' bus='ide'/>
      <address type='drive' controller='0' bus='0' target='0' unit='0'/>
    </disk>
```
开启虚拟机，并查看状态：
```
virsh start Metasploitable2-Linux
virsh list --all
```

xp参照这个https://blog.csdn.net/tianci_zzz/article/details/73896955

## windows下
```
:: Convert VMDK to QCOW2
:: 需要 Qemu 和 VMWare
:: (C)DXkite 
:: https://github.com/DXkite
:: vmdk2qcow2.bat vmwaredk   qcow2-name
@echo off

SET VMWARE_HOME=C:\Program Files (x86)\VMware\VMware Workstation
SET QEMU_HOME=C:\Program Files\qemu

SET VMDK_Manager= "%VMWARE_HOME%\vmware-vdiskmanager.exe"
SET QEMU_IMG= "%QEMU_HOME%\qemu-img.exe"

echo Step1: repair disk image
%VMDK_Manager% -R %1 

echo Step2: convert to one disk file %2.vmdk
%VMDK_Manager% -r %1 -t 0 %2.vmdk

echo Step3: convert to qcow2
%QEMU_IMG% convert -c -o compat=0.10 -f vmdk -O qcow2  %2.vmdk %2

echo conversion successful! QCOW2 file saved on %2.
```
