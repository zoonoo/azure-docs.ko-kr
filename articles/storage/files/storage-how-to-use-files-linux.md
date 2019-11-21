---
title: Linux에서 Azure Files 사용 | Microsoft Docs
description: Linux에서 SMB를 통해 Azure 파일 공유를 탑재하는 방법을 알아봅니다.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/19/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 3d8d7c6d3c4e752480310c122bcb7db237b3022b
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2019
ms.locfileid: "74209409"
---
# <a name="use-azure-files-with-linux"></a>Linux에서 Azure Files 사용
[Azure Files](storage-files-introduction.md)는 사용하기 쉬운 Microsoft 클라우드 파일 시스템입니다. Azure 파일 공유는 [SMB 커널 클라이언트](https://wiki.samba.org/index.php/LinuxCIFS)를 사용하여 Linux 배포판에 탑재할 수 있습니다. 이 문서에서는 Azure 파일 공유를 탑재하는 두 가지 방법을 보여 줍니다. 하나는 요청 시 `mount` 명령을 사용하여 탑재하고, 다른 하나는 `/etc/fstab`에 항목을 만들어 부팅 시 탑재하는 방법입니다.

The recommended way to mount an Azure file share on Linux is using SMB 3.0. By default, Azure Files requires encryption in transit, which is only supported by SMB 3.0. Azure Files also supports SMB 2.1, which does not support encryption in transit, but you may not mount Azure file shares with SMB 2.1 from another Azure region or on-premises for security reasons. Unless your application specifically requires SMB 2.1, there is little reason to use it since most popular, recently released Linux distributions support SMB 3.0:  

| | SMB 2.1 <br>(동일한 Azure 지역 내에서 VM에 탑재) | SMB 3.0 <br>(온-프레미스 및 지역 간 탑재) |
| --- | :---: | :---: |
| Ubuntu | 14.04+ | 16.04+ |
| RHEL(Red Hat Enterprise Linux) | 7+ | 7.5+ |
| CentOS | 7+ |  7.5+ |
| Debian | 8+ | 10+ |
| openSUSE | 13.2+ | 42.3+ |
| SUSE Linux Enterprise Server | 12+ | 12 SP3+ |

If you're using a Linux distribution not listed in the above table, you can check to see if your Linux distribution supports SMB 3.0 with encryption by checking the Linux kernel version. SMB 3.0 with encryption was added to Linux kernel version 4.11. The `uname` command will return the version of the Linux kernel in use:

```bash
uname -r
```

## <a name="prerequisites"></a>전제 조건
<a id="smb-client-reqs"></a>

* <a id="install-cifs-utils"></a>**Ensure the cifs-utils package is installed.**  
    cifs-utils는 원하는 Linux 배포판의 패키지 관리자를 사용하여 설치할 수 있습니다. 

    **Ubuntu** 및 **Debian 기반** 배포판에서는 `apt` 패키지 관리자를 사용합니다.

    ```bash
    sudo apt update
    sudo apt install cifs-utils
    ```

    On **Fedora**, **Red Hat Enterprise Linux 8+** , and **CentOS 8 +** , use the `dnf` package manager:

    ```bash
    sudo dnf install cifs-utils
    ```

    On older versions of **Red Hat Enterprise Linux** and **CentOS**, use the `yum` package manager:

    ```bash
    sudo yum install cifs-utils 
    ```

    **openSUSE**에서는 `zypper` 패키지 관리자를 사용합니다.

    ```bash
    sudo zypper install cifs-utils
    ```

    다른 배포판에서는 적절한 패키지 관리자를 사용하거나 [소스에서 컴파일합니다](https://wiki.samba.org/index.php/LinuxCIFS_utils#Download).

* **The most recent version of the Azure Command Line Interface (CLI).** For more information on how to install the Azure CLI, see [Install the Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) and select your operating system. If you prefer to use the Azure PowerShell module in PowerShell 6+, you may, however the instructions below are presented for the Azure CLI.

* **445 포트가 열려 있는지 확인합니다**. SMB는 445 TCP 포트를 통해 통신합니다. 방화벽이 클라이언트 컴퓨터에서 445 TCP 포트를 차단하고 있지 않은지 확인합니다.  Replace **<your-resource-group>** and **<your-storage-account>**
    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    
    # This command assumes you have logged in with az login
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))
    fileHost=$(echo $fileHost | tr -d "/")

    nc -zvw3 $fileHost 445
    ```

    If the connection was successful, you should see something similar to the following output:

    ```
    Connection to <your-storage-account> 445 port [tcp/microsoft-ds] succeeded!
    ```

    If you are unable to open up port 445 on your corporate network or are blocked from doing so by an ISP, you may use a VPN connection or ExpressRoute to work around port 445. For more information, see [Networking considerations for direct Azure file share access](storage-files-networking-overview.md)..

## <a name="mounting-azure-file-share"></a>Mounting Azure file share
To use an Azure file share with your Linux distribution, you must create a directory to serve as the mount point for the Azure file share. A mount point can be created anywhere on your Linux system, but it's common convention to create this under /mnt. After the mount point, you use the `mount` command to access the Azure file share.

You can mount the same Azure file share to multiple mount points if desired.

### <a name="mount-the-azure-file-share-on-demand-with-mount"></a>요청 시 `mount`를 사용하여 Azure 파일 공유 탑재
1. **Create a folder for the mount point**: Replace `<your-resource-group>`, `<your-storage-account>`, and `<your-file-share>` with the appropriate information for your environment:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **Use the mount command to mount the Azure file share**. In the example below, the local Linux file and folder permissions default 0755, which means read, write, and execute for the owner (based on the file/directory Linux owner), read and execute for users in owner group, and read and execute for others on the system. You can use the `uid` and `gid` mount options to set the user ID and group ID for the mount. You can also use `dir_mode` and `file_mode` to set custom permissions as desired. For more information on how to set permissions, see [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) on Wikipedia. 

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')

    sudo mount -t cifs $smbPath $mntPath -o vers=3.0,username=$storageAccountName,password=$storageAccountKey,serverino
    ```

    > [!Note]  
    > The above mount command mounts with SMB 3.0. If your Linux distribution does not support SMB 3.0 with encryption or if it only supports SMB 2.1, you may only mount from an Azure VM within the same region as the storage account. To mount your Azure file share on a Linux distribution that does not support SMB 3.0 with encryption, you will need to [disable encryption in transit for the storage account](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

Azure 파일 공유를 사용하여 작업을 완료하면 `sudo umount $mntPath`를 사용하여 공유를 탑재 해제할 수 있습니다.

### <a name="create-a-persistent-mount-point-for-the-azure-file-share-with-etcfstab"></a>`/etc/fstab`을 사용하여 Azure 파일 공유에 대한 영구 탑재 지점 만들기
1. **Create a folder for the mount point**: A folder for a mount point can be created anywhere on the file system, but it's common convention to create this under /mnt. For example, the following command creates a new directory, replace `<your-resource-group>`, `<your-storage-account>`, and `<your-file-share>` with the appropriate information for your environment:

    ```bash
    resourceGroupName="<your-resource-group>"
    storageAccountName="<your-storage-account>"
    fileShareName="<your-file-share>"

    mntPath="/mnt/$storageAccountName/$fileShareName"

    sudo mkdir -p $mntPath
    ```

1. **파일 공유를 위한 사용자 이름(스토리지 계정 이름) 및 암호(스토리지 계정 키)를 저장할 자격 증명 파일을 만듭니다.** 

    ```bash
    if [ ! -d "/etc/smbcredentials" ]; then
        sudo mkdir "/etc/smbcredentials"
    fi

    storageAccountKey=$(az storage account keys list \
        --resource-group $resourceGroupName \
        --account-name $storageAccountName \
        --query "[0].value" | tr -d '"')
    
    smbCredentialFile="/etc/smbcredentials/$storageAccountName.cred"
    if [ ! -f $smbCredentialFile ]; then
        echo "username=$storageAccountName" | sudo tee $smbCredentialFile > /dev/null
        echo "password=$storageAccountKey" | sudo tee -a $smbCredentialFile > /dev/null
    else 
        echo "The credential file $smbCredentialFile already exists, and was not modified."
    fi
    ```

1. **하나의 루트만 암호 파일을 읽거나 수정할 수 있도록 자격 증명 파일의 권한을 변경합니다.** 스토리지 계정 키는 기본적으로 스토리지 계정에 대한 상위 관리자 암호이므로, 루트만 액세스할 수 있는 파일에서 사용 권한을 설정하는 것은 더 낮은 권한 사용자가 스토리지 계정 키를 검색할 수 없도록 하는 데 중요합니다.   

    ```bash
    sudo chmod 600 $smbCredentialFile
    ```

1. **Use the following command to append the following line to `/etc/fstab`** : In the example below, the local Linux file and folder permissions default 0755, which means read, write, and execute for the owner (based on the file/directory Linux owner), read and execute for users in owner group, and read and execute for others on the system. You can use the `uid` and `gid` mount options to set the user ID and group ID for the mount. You can also use `dir_mode` and `file_mode` to set custom permissions as desired. For more information on how to set permissions, see [UNIX numeric notation](https://en.wikipedia.org/wiki/File_system_permissions#Numeric_notation) on Wikipedia.

    ```bash
    httpEndpoint=$(az storage account show \
        --resource-group $resourceGroupName \
        --name $storageAccountName \
        --query "primaryEndpoints.file" | tr -d '"')
    smbPath=$(echo $httpEndpoint | cut -c7-$(expr length $httpEndpoint))$fileShareName

    if [ -z "$(grep $smbPath\ $mntPath /etc/fstab)" ]; then
        echo "$smbPath $mntPath cifs nofail,vers=3.0,credentials=$smbCredentialFile,serverino" | sudo tee -a /etc/fstab > /dev/null
    else
        echo "/etc/fstab was not modified to avoid conflicting entries as this Azure file share was already present. You may want to double check /etc/fstab to ensure the configuration is as desired."
    fi

    sudo mount -a
    ```
    
    > [!Note]  
    > The above mount command mounts with SMB 3.0. If your Linux distribution does not support SMB 3.0 with encryption or if it only supports SMB 2.1, you may only mount from an Azure VM within the same region as the storage account. To mount your Azure file share on a Linux distribution that does not support SMB 3.0 with encryption, you will need to [disable encryption in transit for the storage account](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

## <a name="securing-linux"></a>Securing Linux
In order to mount an Azure file share on Linux, port 445 must be accessible. 많은 조직에서 SMB 1에 내재된 보안 위험 때문에 포트 445를 차단합니다. SMB 1, also known as CIFS (Common Internet File System), is a legacy file system protocol included with many Linux distributions. SMB 1은 구식 프로토콜로 비효율적이며 무엇보다도 보안성이 떨어집니다. The good news is that Azure Files does not support SMB 1, and starting with Linux kernel version 4.18, Linux makes it possible to disable SMB 1. We always [strongly recommend](https://aka.ms/stopusingsmb1) disabling the SMB 1 on your Linux clients before using SMB file shares in production.

Starting with Linux kernel 4.18, the SMB kernel module, called `cifs` for legacy reasons, exposes a new module parameter (often referred to as *parm* by various external documentation), called `disable_legacy_dialects`. Although introduced in Linux kernel 4.18, some vendors have backported this change to older kernels that they support. For convenience, the following table details the availability of this module parameter on common Linux distributions.

| 유통 | Can disable SMB 1 |
|--------------|-------------------|
| Ubuntu 14.04-16.04 | 아닙니다. |
| Ubuntu 18.04 | yes |
| Ubuntu 19.04+ | yes |
| Debian 8-9 | 아닙니다. |
| Debian 10+ | yes |
| Fedora 29+ | yes |
| CentOS 7 | 아닙니다. | 
| CentOS 8+ | yes |
| Red Hat Enterprise Linux 6.x-7.x | 아닙니다. |
| Red Hat Enterprise Linux 8+ | yes |
| openSUSE Leap 15.0 | 아닙니다. |
| openSUSE Leap 15.1+ | yes |
| openSUSE Tumbleweed | yes |
| SUSE Linux Enterprise 11.x-12.x | 아닙니다. |
| SUSE Linux Enterprise 15 | 아닙니다. |
| SUSE Linux Enterprise 15.1 | 아닙니다. |

You can check to see if your Linux distribution supports the `disable_legacy_dialects` module parameter via the following command.

```bash
sudo modinfo -p cifs | grep disable_legacy_dialects
```

This command should output the following message:

```Output
disable_legacy_dialects: To improve security it may be helpful to restrict the ability to override the default dialects (SMB2.1, SMB3 and SMB3.02) on mount with old dialects (CIFS/SMB1 and SMB2) since vers=1.0 (CIFS/SMB1) and vers=2.0 are weaker and less secure. Default: n/N/0 (bool)
```

Before disabling SMB 1, you must check to make sure that the SMB module is not currently loaded on your system (this happens automatically if you have mounted an SMB share). You can do this with the following command, which should output nothing if SMB is not loaded:

```bash
lsmod | grep cifs
```

To unload the module, first unmount all SMB shares (using the `umount` command as described above). You can identify all the mounted SMB shares on your system with the following command:

```bash
mount | grep cifs
```

Once you have unmounted all SMB file shares, it's safe to unload the module. 이렇게 하려면 `modprobe` 명령을 사용합니다.

```bash
sudo modprobe -r cifs
```

You can manually load the module with SMB 1 unloaded using the `modprobe` command:

```bash
sudo modprobe cifs disable_legacy_dialects=Y
```

Finally, you can check the SMB module has been loaded with the parameter by looking at the loaded parameters in `/sys/module/cifs/parameters`:

```bash
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

To persistently disable SMB 1 on Ubuntu and Debian-based distributions, you must create a new file (if you don't already have custom options for other modules) called `/etc/modprobe.d/local.conf` with the setting. You can do this with the following command:

```bash
echo "options cifs disable_legacy_dialects=Y" | sudo tee -a /etc/modprobe.d/local.conf > /dev/null
```

You can verify that this has worked by loading the SMB module:

```bash
sudo modprobe cifs
cat /sys/module/cifs/parameters/disable_legacy_dialects
```

## <a name="feedback"></a>피드백
Linux 사용자 여러분의 의견을 듣고 싶습니다!

Linux 사용자 그룹용 Azure Files는 Linux에서 File Storage를 평가하고 채택할 때 피드백을 공유할 수 있도록 포럼을 제공합니다. 사용자 그룹에 참가하려면 [Azure Files Linux 사용자](mailto:azurefileslinuxusers@microsoft.com)에게 메일을 보내세요.

## <a name="next-steps"></a>다음 단계
Azure Files에 대한 자세한 내용은 다음 링크를 참조하세요.

* [Azure 파일 배포에 대한 계획](storage-files-planning.md)
* [FAQ](../storage-files-faq.md)
* [문제 해결](storage-troubleshoot-linux-file-connection-problems.md)
