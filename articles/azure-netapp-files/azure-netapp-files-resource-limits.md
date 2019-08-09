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
ms.date: 08/07/2019
ms.author: b-juche
ms.openlocfilehash: 763b3961bfd621f05ab5cd021526b0d6d109c020
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881717"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 한계

다음 표에서는 Azure NetApp Files에 대 한 리소스 제한을 설명 합니다.

|  리소스  |  기본 제한  |  지원 요청을 통해 조정 가능  |
|----------------|---------------------|--------------------------------------|
|  Azure 구독 당 NetApp 계정 수   |  10    |  예   |
|  NetApp 계정 당 용량 풀 수   |    25     |   예   |
|  용량 풀 당 볼륨 수     |    500   |    예     |
|  볼륨당 스냅숏 수       |    255     |    아니요        |
|  Azure Virtual Network Azure NetApp Files (Microsoft NetApp/볼륨)에 위임 된 서브넷 수    |   1   |    아니요    |
|  Azure NetApp Files에서 VNet (피어 링 Vnet 포함)에 사용 중인 최대 Ip 수    |    1000   |    아니요   |
|  단일 용량 풀의 최소 크기   |  4TiB     |    아니요  |
|  단일 용량 풀의 최대 크기    |  500TiB   |   아니요   |
|  단일 볼륨의 최소 크기    |    100GiB    |    아니요    |
|  단일 볼륨에 할당 된 최대 할당량 *   |   92 TiB   |    아니요   |
|  단일 볼륨의 최대 크기 *     |    100 TiB    |    아니요       |
|  볼륨당 최대 파일 수 (inode)     |    5000만    |    아니요    |    

\* 볼륨을 수동으로 만들거나 크기를 최대 92 TiB로 조정할 수 있습니다. 그러나 초과분 시나리오에서는 볼륨이 최대 100 TiB 증가할 수 있습니다. 용량 초과분에 대 한 자세한 내용은 [Azure NetApp Files에 대 한 비용 모델](azure-netapp-files-cost-model.md) 을 참조 하세요. 

## <a name="request-limit-increase"></a>요청 제한 증가 

Azure 지원 요청을 만들어 위의 표에서 조정 가능한 제한을 늘릴 수 있습니다. 

Azure Portal 탐색 평면에서: 

1. **도움말 + 지원**을 클릭 합니다.
2. **+ 새 지원 요청**을 클릭 합니다.
3. 기본 사항 탭에서 다음 정보를 제공 합니다. 
    1. 문제 유형: **서비스 및 구독 제한 (할당량)** 을 선택 합니다.
    2. 구독: 할당량이 증가 해야 하는 리소스에 대 한 구독을 선택 합니다.
    3. 할당량 유형: 저장소 **선택: Azure NetApp Files 제한**.
    4. **다음: 솔루션**.
4. 세부 정보 탭에서 다음을 수행 합니다.
    1. 설명 상자에 해당 하는 리소스 종류에 대 한 다음 정보를 제공 합니다.

        |  리소스  |    부모 리소스      |    요청 된 새 제한     |    할당량 증가 이유       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  계정 |  *구독 ID*   |  *요청 된 새 최대 **계정** 번호*    |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |
        |  풀    |  *구독 ID, 계정 URI*  |  *요청 된 새 최대 **풀** 번호*   |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |
        |  볼륨  |  *구독 ID, 계정 URI, 풀 URI*   |  *요청 된 새 최대 **볼륨** 번호*     |  *요청을 확인 하는 시나리오 또는 사용 사례는 무엇입니까?*  |

    2. 적절 한 지원 방법을 지정 하 고 계약 정보를 제공 합니다.

    3. **다음: 검토 + 만들기** 를 참조 하 여 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files에 대 한 비용 모델](azure-netapp-files-cost-model.md)
