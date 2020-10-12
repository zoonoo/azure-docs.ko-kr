---
title: Azure NetApp Files에 대 한 수동 QoS 용량 풀 관리 Microsoft Docs
description: 수동 qos 용량 풀을 설정 하 고 수동 QoS를 사용 하도록 용량 풀을 변경 하는 등 수동 QoS 유형을 사용 하는 용량 풀을 관리 하는 방법을 설명 합니다.
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
ms.date: 09/28/2020
ms.author: b-juche
ms.openlocfilehash: d2abb66684738e97ade2a2e67309a4e7b53c4734
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91442089"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>수동 QoS 용량 풀 관리

이 문서에서는 수동 QoS 유형을 사용 하는 용량 풀을 관리 하는 방법을 설명 합니다.  

QoS 유형에 대 한 고려 사항을 이해 하려면 [Azure NetApp Files의 저장소 계층 구조](azure-netapp-files-understand-storage-hierarchy.md) 및 [Azure NetApp Files에 대 한 성능 고려 사항](azure-netapp-files-performance-considerations.md) 을 참조 하세요.  

## <a name="register-the-feature"></a>기능 등록
수동 QoS 유형 기능은 현재 미리 보기 상태입니다. 이 기능을 처음 사용 하는 경우 먼저 기능을 등록 해야 합니다.
  
1.  기능을 등록 합니다.

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. 기능 등록의 상태를 확인 합니다. 

    > [!NOTE]
    > **RegistrationState** `Registering` 로 변경 하기 전까지 최대 60 분 동안 registrationstate 상태가 될 수 있습니다 `Registered` . 계속 하기 전에 상태가 **등록** 될 때까지 기다립니다.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
[Azure CLI 명령을](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest&preserve-view=true) 사용 하 여 `az feature register` 기능을 `az feature show` 등록 하 고 등록 상태를 표시할 수도 있습니다. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>새 수동 QoS 용량 풀 설정 

수동 QoS 유형을 사용 하 여 새 용량 풀을 만들려면 다음을 수행 합니다.

1. [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)의 단계를 따릅니다.  

2. 새 용량 풀 창에서 **수동 QoS** 유형을 선택 합니다.  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>수동 QoS를 사용 하도록 용량 풀 변경

수동 QoS 유형을 사용 하기 위해 현재 자동 QoS 유형을 사용 하는 용량 풀을 변경할 수 있습니다.  

> [!IMPORTANT]
> 용량 유형을 수동 QoS로 설정 하는 것은 영구적으로 변경 된 것입니다. 수동 QoS 유형 용량 도구를 자동 QoS 용량 풀로 변환할 수 없습니다. 

1. NetApp 계정에 대 한 관리 블레이드에서 **용량 풀** 을 클릭 하 여 기존 용량 풀을 표시 합니다.   
 
2.  수동 QoS를 사용 하 여 변경 하려는 용량 풀을 클릭 합니다.

3.  **QoS 유형 변경**을 클릭 합니다. 그런 다음 **새 QoS 유형** 을 **수동**으로 설정 합니다. **확인**을 클릭합니다. 

![QoS 유형 변경](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>수동 QoS 용량 풀의 처리량 모니터링  

메트릭은 볼륨의 읽기 및 쓰기 처리량을 모니터링 하는 데 사용할 수 있습니다.  [Azure NetApp Files에 대 한 메트릭을](azure-netapp-files-metrics.md)참조 하세요.  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>수동 QoS 볼륨의 할당 된 처리량 수정 

볼륨이 수동 QoS 용량 풀에 포함 되어 있는 경우 필요에 따라 할당 된 볼륨 처리량을 수정할 수 있습니다.

1. **볼륨** 페이지에서 처리량을 수정 하려는 볼륨을 선택 합니다.   

2. **처리량 변경**을 클릭 합니다. 원하는 **처리량 (MiB/S)** 을 지정 합니다. **확인**을 클릭합니다. 

    ![QoS 처리량 변경](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>다음 단계  

* [용량 풀 설정](azure-netapp-files-set-up-capacity-pool.md)
* [Azure NetApp Files에 대한 메트릭](azure-netapp-files-metrics.md)
* [Azure NetApp Files의 성능 고려 사항](azure-netapp-files-performance-considerations.md)
* [용량 풀 문제 해결](troubleshoot-capacity-pools.md)
* [Azure NetApp Files의 스토리지 계층 구조](azure-netapp-files-understand-storage-hierarchy.md)
* [Azure NetApp Files에 대한 서비스 수준](azure-netapp-files-service-levels.md)
* [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
* [Azure NetApp Files에 대한 리소스 제한](azure-netapp-files-resource-limits.md)
* [NFS 볼륨 만들기](azure-netapp-files-create-volumes.md)
* [SMB 볼륨 만들기](azure-netapp-files-create-volumes-smb.md)
* [이중 프로토콜 볼륨 만들기](create-volumes-dual-protocol.md)


