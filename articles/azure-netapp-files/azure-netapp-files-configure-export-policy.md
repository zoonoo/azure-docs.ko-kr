---
title: NFS 볼륨에 대 한 내보내기 정책 구성-Azure NetApp Files
description: Azure NetApp Files를 사용 하 여 NFS 볼륨에 대 한 액세스를 제어 하도록 내보내기 정책을 구성 하는 방법을 설명 합니다.
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 4a20a223932f82c80ad5831ef3a02bad803e26e6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "87533213"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>NFS 볼륨에 대한 내보내기 정책 구성

Azure NetApp Files 볼륨에 대 한 액세스를 제어 하도록 내보내기 정책을 구성할 수 있습니다. Azure NetApp Files 내보내기 정책은 NFS 프로토콜 (NFSv3 및 NFSv 4.1 모두) 및 이중 프로토콜 (NFSv3 및 SMB)을 사용 하는 볼륨을 지원 합니다. 

최대 5 개의 내보내기 정책 규칙을 만들 수 있습니다.

## <a name="steps"></a>단계 

1.  볼륨 페이지에서 내보내기 정책을 구성 하려는 볼륨을 선택 하 고 **정책 내보내기**를 클릭 합니다. 

    또한 볼륨을 만드는 동안 내보내기 정책을 구성할 수 있습니다.

2.  내보내기 정책 규칙을 만들도록 다음 필드에 대한 정보를 지정합니다.   
    *  **인덱싱할**   
        규칙에 대한 인덱스 번호를 지정합니다.  
        내보내기 정책은 최대 5개의 규칙으로 구성할 수 있습니다. 규칙은 인덱스 번호 목록의 해당 순서에 따라 평가됩니다. 더 낮은 인덱스 번호의 규칙이 먼저 평가됩니다. 예를 들어 인덱스 번호가 1인 규칙은 인덱스 번호가 2인 규칙보다 먼저 계산됩니다. 

    * **허용 된 클라이언트**   
        다음 형식 중 하나에 값을 지정합니다.  
        * IPv4 주소(예: `10.1.12.24`) 
        * 비트 수로 표현된 서브넷 마스크가 있는 IPv4 주소(예: `10.1.12.10/4`)

    * **Access**  
        다음 액세스 유형 중 하나를 선택합니다.  
        * 액세스 권한 없음 
        * 읽기 및 쓰기
        * 읽기 전용

    * **읽기** 전용 및 **읽기/쓰기**  
        NFSv 4.1에서 Kerberos 암호화를 사용 하는 경우 [nfsv 4.1 kerberos 암호화 구성](configure-kerberos-encryption.md)의 지침을 따르세요.  Kerberos의 성능에 미치는 영향에 대 한 자세한 내용은 [nfsv 4.1의 Kerberos 성능 영향](configure-kerberos-encryption.md#kerberos_performance)을 참조 하세요. 

        ![Kerberos 보안 옵션](../media/azure-netapp-files/kerberos-security-options.png) 

    * **루트 액세스**  
        `root`계정에서 볼륨에 액세스할 수 있는지 여부를 지정 합니다.  기본적으로 루트 액세스는 **On**으로 설정 되 고, 계정에는 볼륨에 대 한 액세스 권한이 있습니다 `root` .

![내보내기 정책](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>다음 단계 
* [가상 머신에 대한 볼륨 탑재 또는 분리](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [스냅샷 관리](azure-netapp-files-manage-snapshots.md)
