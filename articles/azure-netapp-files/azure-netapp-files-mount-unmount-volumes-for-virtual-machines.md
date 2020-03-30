---
title: 가상 시스템에 대한 Azure NetApp 파일 볼륨 마운트
description: Azure에서 Windows 가상 컴퓨터 또는 Linux 가상 컴퓨터에 대한 볼륨을 마운트하거나 마운트 해제하는 방법에 대해 알아봅니다.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 12/05/2019
ms.openlocfilehash: 3457adf67fa067a154eef008b08d8cfcc1d9eaa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/27/2020
ms.locfileid: "75551542"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제 

필요에 따라 Windows 또는 Linux 가상 머신에 대한 볼륨을 탑재 또는 탑재 해제할 수 있습니다.  Linux 가상 머신에 대한 탑재 지침은 Azure NetApp Files에서 사용할 수 있습니다.  

1. **볼륨** 블레이드를 클릭한 다음 마운트할 볼륨을 선택합니다. 
2. 선택한 볼륨에서 **마운트 지침을** 클릭한 다음 지침에 따라 볼륨을 마운트합니다. 

    ![마운트 지침 NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![마운트 지침 SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    NFSv4.1을 사용하는 경우 다음 명령을 사용하여 파일 시스템을 탑재합니다.`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Azure VM을 시작하거나 재부팅할 때 NFS 볼륨을 자동으로 탑재하려면 호스트의 `/etc/fstab` 파일에 항목을 추가합니다. 

    예: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`은 볼륨 속성 블레이드에 있는 Azure NetApp 파일 볼륨의 IP 주소입니다.
    * `$FILEPATH`은 Azure NetApp 파일 볼륨의 내보내기 경로입니다.
    * `$MOUNTPOINT`는 NFS 내보내기를 탑재하는 데 사용되는 Linux 호스트에서 만든 디렉터리입니다.

4. NFS를 사용하여 볼륨을 Windows에 탑재하려면 다음을 수행하십시오.

    a. 먼저 유닉스 또는 리눅스 VM에 볼륨을 탑재합니다.  
    b. 볼륨에 `chmod 777` `chmod 775` 대해 또는 명령을 실행합니다.  
    다. Windows의 NFS 클라이언트를 통해 볼륨을 마운트합니다.

## <a name="next-steps"></a>다음 단계

* [Azure NetApp Files에 대한 NFSv 4.1 기본 도메인 구성](azure-netapp-files-configure-nfsv41-domain.md)
* [NFS 자주 묻는 질문](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
