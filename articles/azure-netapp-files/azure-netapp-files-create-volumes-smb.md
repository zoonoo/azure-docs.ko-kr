---
title: Azure NetApp Files에 대한 SMB 볼륨 만들기 | Microsoft Docs
description: 이 문서에서는 Azure NetApp Files에서 SMB3 볼륨을 만드는 방법을 보여 줍니다. Active Directory 연결 및 도메인 서비스에 대 한 요구 사항에 대해 알아봅니다.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 02/16/2021
ms.author: b-juche
ms.openlocfilehash: 91f4f90658281282cdcb01b091bd9c9647d8d702
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/17/2021
ms.locfileid: "100635492"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Azure NetApp Files에 대한 SMB 볼륨 만들기

Azure NetApp Files에서는 NFS (NFSv3 및 NFSv 4.1), SMB3 또는 이중 프로토콜 (NFSv3 및 SMB)을 사용 하 여 볼륨을 만들 수 있습니다. 볼륨의 용량 소비는 해당 풀의 프로비전된 용량에 대해 계산됩니다. 이 문서에서는 SMB3 볼륨을 만드는 방법을 보여 줍니다.

## <a name="before-you-begin"></a>시작하기 전에 

* 용량 풀을 설정해야 합니다. [용량 풀 설정을](azure-netapp-files-set-up-capacity-pool.md)참조 하세요.     
* Azure NetApp Files에 서브넷을 위임해야 합니다. [Azure NetApp Files에 서브넷 위임을](azure-netapp-files-delegate-subnet.md)참조 하세요.

## <a name="configure-active-directory-connections"></a>Active Directory 연결 구성 

SMB 볼륨을 만들기 전에 Active Directory 연결을 만들어야 합니다. Azure NetApp 파일에 대해 Active Directory 연결을 구성 하지 않은 경우 [Active Directory 연결 만들기 및 관리](create-active-directory-connections.md)에 설명 된 지침을 따르세요.

## <a name="add-an-smb-volume"></a>SMB 볼륨 추가

1. 용량 풀 블레이드에서 **볼륨** 블레이드를 클릭합니다. 

    ![볼륨으로 이동](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. **+ 볼륨 추가** 를 클릭하여 볼륨을 만듭니다.  
    볼륨 만들기 창이 나타납니다.

3. 볼륨 만들기 창에서 **만들기** 를 클릭 하 고 기본 사항 탭에서 다음 필드에 대 한 정보를 제공 합니다.   
    * **볼륨 이름**      
        만들고 있는 볼륨의 이름을 지정합니다.   

        볼륨 이름은 각 용량 풀 내에서 고유 해야 합니다. 3자 이상이어야 합니다. 영숫자 문자를 사용할 수 있습니다.   

        `default`또는를 `bin` 볼륨 이름으로 사용할 수 없습니다.

    * **용량 풀**  
        볼륨을 만들 용량 풀을 지정합니다.

    * **할당량**  
        볼륨에 할당되는 논리 스토리지의 크기를 지정합니다.  

        **사용 가능한 할당량** 필드는 새 볼륨을 만들 때 사용할 수 있는 선택한 용량 풀에서 사용되지 않은 공간의 양을 보여줍니다. 새 볼륨의 크기는 사용 가능한 할당량을 초과해서는 안 됩니다.  

    * **처리량 (MiB/S)**   
        볼륨이 수동 QoS 용량 풀에 생성 되 면 볼륨에 대해 원하는 처리량을 지정 합니다.   

        볼륨이 자동 QoS 용량 풀에 생성 되는 경우이 필드에 표시 되는 값은 (할당량 x 서비스 수준 처리량)입니다.   

    * **가상 네트워크**  
        볼륨에 액세스하려는 Microsoft Azure Virtual Network(VNet)를 지정합니다.  

        지정하는 VNet에는 Azure NetApp Files에 위임된 서브넷이 있어야 합니다. Azure NetApp Files 서비스는 동일한 VNet 또는 VNet 피어링을 통해 볼륨과 동일한 영역에 있는 VNet에서만 액세스할 수 있습니다. Express Route를 통해 온-프레미스 네트워크에서 볼륨에 액세스할 수도 있습니다.   

    * **서브넷**  
        볼륨에 사용할 서브넷을 지정합니다.  
        지정하는 서브넷은 Azure NetApp Files에 위임되어야 합니다. 
        
        서브넷을 위임하지 않은 경우 볼륨 만들기 페이지에서 **새로 만들기** 를 클릭할 수 있습니다. 그런 다음, 서브넷 만들기 페이지에서 서브넷 정보를 지정하고 **Microsoft.NetApp/volumes** 를 선택하여 Azure NetApp Files의 서브넷을 위임합니다. 각 VNet에서 하나의 서브넷만 Azure NetApp Files에 위임할 수 있습니다.   
 
        ![볼륨 만들기](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![서브넷 만들기](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * 볼륨에 기존 스냅숏 정책을 적용 하려면 **고급 섹션 표시** 를 클릭 하 여 확장 하 고, 스냅숏 경로를 숨길지 여부를 지정 하 고, 풀 다운 메뉴에서 스냅숏 정책을 선택 합니다. 

        스냅숏 정책을 만드는 방법에 대 한 자세한 내용은 [스냅숏 정책 관리](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)를 참조 하세요.

        ![고급 선택 표시](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. **프로토콜** 을 클릭하고 다음 정보를 입력합니다.  
    * 볼륨의 프로토콜 유형으로 **SMB** 를 선택합니다. 
    * 드롭다운 목록에서 **Active Directory** 연결을 선택합니다.
    * **공유 이름** 에 공유 볼륨의 이름을 지정합니다.

    ![SMB 프로토콜 지정](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. **검토 + 만들기** 를 클릭하여 볼륨 정보를 검토합니다.  그런 다음, **만들기** 를 클릭하여 SMB 볼륨을 만듭니다.

    만든 볼륨이 볼륨 페이지에 표시됩니다. 
 
    볼륨은 해당 용량 풀에서 구독, 리소스 그룹, 위치 특성을 상속합니다. 볼륨 배포 상태를 모니터링하려면 알림 탭을 사용할 수 있습니다.

## <a name="control-access-to-an-smb-volume"></a>SMB 볼륨에 대한 액세스 제어  

SMB 볼륨에 대한 액세스는 사용 권한을 통해 관리합니다.  

### <a name="share-permissions"></a>공유 사용 권한  

기본적으로 새 볼륨에는 **모든 사람/모든 권한** 공유 권한이 있습니다. 도메인 관리자 그룹의 구성원은 Azure NetApp Files 볼륨에 사용되는 컴퓨터 계정에서 컴퓨터 관리를 사용하여 공유 사용 권한을 변경할 수 있습니다.

![SMB 탑재 경로](../media/azure-netapp-files/smb-mount-path.png) 
![공유 사용 권한 설정](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>NTFS 파일 및 폴더 사용 권한  

Windows SMB 클라이언트에서 개체 속성의 **보안** 탭을 사용하여 파일 또는 폴더에 대한 사용 권한을 설정할 수 있습니다.
 
![파일 및 폴더 사용 권한 설정](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>다음 단계  

* [Windows 또는 Linux 가상 머신에 대한 볼륨 탑재 또는 탑재 해제](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [SMB FAQ](./azure-netapp-files-faqs.md#smb-faqs)
* [SMB 또는 이중 프로토콜 볼륨 문제 해결](troubleshoot-dual-protocol-volumes.md)
* [Azure 서비스에 대한 가상 네트워크 통합에 대해 알아보기](../virtual-network/virtual-network-for-azure-services.md)
* [Azure 명령줄 인터페이스를 사용하여 새 Active Directory 포리스트 설치](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
