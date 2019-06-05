---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: NetApp 계정, 용량 풀, 볼륨, 스냅샷 및 위임된 서브넷에 대한 제한 사항을 포함하여 Azure NetApp Files 리소스에 대한 제한 사항을 설명합니다.
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
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: b-juche
ms.openlocfilehash: b55467d77beb8f97b8e392b72682268ae0407e54
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/17/2019
ms.locfileid: "65826366"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 한계

다음 표에서 Azure NetApp 파일에 대 한 리소스 제한을 설명합니다.

|  Resource  |  기본 제한  |  지원 요청을 통해 조정할 수  |
|----------------|---------------------|--------------------------------------|
|  Azure 구독 당 NetApp 계정 수   |  10    |  예.   |
|  NetApp 계정당 용량 풀 수   |    25     |   예.   |
|  용량 풀 당 볼륨의 수     |    500   |    예.     |
|  볼륨당 스냅숏 수       |    255     |    아닙니다.        |
|  Azure Virtual Network 당 Azure NetApp 파일 (Microsoft.NetApp/volumes)에 위임 하는 서브넷의 수    |   1   |    아닙니다.    |
|  최대 Vm 수 (피어 링 된 Vnet 포함) 볼륨에 연결할 수 있는     |    1000   |    아닙니다.   |
|  단일 용량 풀의 최소 크기   |  4TiB     |    아닙니다.  |
|  단일 용량 풀의 최대 크기    |  500TiB   |   아닙니다.   |
|  단일 볼륨의 최소 크기    |    100GiB    |    아닙니다.    |
|  최대 할당량을 단일 볼륨 * 할당   |   92 TiB   |    아닙니다.   |
|  단일 볼륨 *의 최대 크기     |    100tib    |    아닙니다.       |

* 볼륨을 수동으로 만든 또는 최대로 92 TiB을 조정할 수 있습니다. 그러나 볼륨에는 초과 시나리오에서 최대 100 개의 TiB 증가할 수 있습니다. 참조 [NetApp Azure Files에 대 한 비용 모델](azure-netapp-files-cost-model.md) 용량 초과분에 대 한 자세한 내용은 합니다. 

## <a name="request-limit-increase"></a>제한 증가 요청 

위 테이블에서 조정 제한을 늘리려면 Azure 지원 요청을 만들 수 있습니다. 

Azure portal 탐색 평면에서: 

1. 클릭 **도움말 + 지원**합니다.
2. 클릭 **+ 새 지원 요청**합니다.
3. 기본 사항 탭에서 다음 정보를 제공 합니다. 
    1. 문제 유형: 선택 **서비스 및 구독 제한 (할당량)** 합니다.
    2. 구독: 할당량 증가 해야 하는 리소스에 대 한 구독을 선택 합니다.
    3. 할당량 유형: 선택 **저장소: Azure NetApp 파일 제한**합니다.
    4. **다음: 솔루션**합니다.
4. 세부 정보 탭:
    1. 설명 상자에서 해당 리소스 종류에 대해 다음 정보를 제공 합니다.

        |  Resource  |    부모 리소스      |    요청한 새로운 제한     |    할당량 증가 대 한 설명       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  계좌 |  *구독 ID*   |  *요청 된 새 최대값 **계정** 수*    |  *어떤 시나리오 나 사용 사례에는 요청 메시지가?*  |
        |  풀    |  *구독 ID, 계정 URI*  |  *요청 된 새 최대값 **풀** 수*   |  *어떤 시나리오 나 사용 사례에는 요청 메시지가?*  |
        |  볼륨  |  *구독 ID, URI, 계정 풀 URI*   |  *요청 된 새 최대값 **볼륨** 수*     |  *어떤 시나리오 나 사용 사례에는 요청 메시지가?*  |

    2. 적절 한 메서드를 지원 및 계약 정보를 제공 지정 합니다.

    3. **다음: 검토 + 만들기** 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp 파일에 대 한 비용된 모델](azure-netapp-files-cost-model.md)
