---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: Azure NetApp Files 리소스에 대한 제한과 리소스 제한을 늘리는 요청 방법을 설명합니다.
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
ms.date: 03/30/2021
ms.author: b-juche
ms.openlocfilehash: 9b061184f97abeea79912aadbae2c2b188206c72
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/31/2021
ms.locfileid: "106058003"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 제한

다음 표에서는 Azure NetApp Files에 대한 리소스 제한을 설명합니다.

|  리소스  |  기본 제한  |  지원 요청을 통해 조정 가능  |
|----------------|---------------------|--------------------------------------|
|  구독당 Azure 지역별 NetApp 계정 수  |  10    |  예   |
|  NetApp 계정당 용량 풀 수   |    25     |   예   |
|  구독당 볼륨 수   |    500     |   예   |
|  용량 풀당 볼륨 수     |    500   |    예     |
|  볼륨당 스냅샷 수       |    255     |    No        |
|  Azure Virtual Network당 Azure NetApp Files(Microsoft.NetApp/볼륨)에 위임된 서브넷 수    |   1   |    예    |
|  Azure NetApp Files를 사용하는 VNet(즉시 피어링된 VNet 포함)의 사용된 IP 수   |    1000   |    No   |
|  단일 용량 풀의 최소 크기   |  4TiB     |    No  |
|  단일 용량 풀의 최대 크기    |  500TiB   |   No   |
|  단일 볼륨의 최소 크기    |    100GiB    |    아니요    |
|  단일 볼륨의 최대 크기     |    100TiB    |    No    |
|  단일 파일의 최대 크기     |    16TiB    |    No    |    
|  단일 디렉터리에서 디렉터리 메타데이터의 최대 크기      |    320MB    |    No    |    
|  볼륨당 최대 파일 수([maxfiles](#maxfiles))     |    1억    |    예    |    
|  볼륨당 최대 내보내기 정책 규칙 수     |    5  |    No    | 
|  수동 QoS 볼륨에 할당된 최소 처리량     |    1MiB/s   |    No    |    
|  수동 QoS 볼륨에 할당된 최대 처리량     |    4,500MiB/s    |    No    |    
|  지역 간 복제 데이터 보호 볼륨(대상 볼륨) 수     |    5    |    예    |     

디렉터리가 디렉터리 메타데이터의 최대 크기 한도(320MB)에 근접하는지 확인하려면 [디렉터리가 한도 크기에 근접하는지 확인하는 방법](azure-netapp-files-faqs.md#how-do-i-determine-if-a-directory-is-approaching-the-limit-size)을 참조하세요.   

자세한 내용은 [용량 관리 FAQ](azure-netapp-files-faqs.md#capacity-management-faqs)를 참조하세요.

## <a name="maxfiles-limits"></a>Maxfiles 한도 <a name="maxfiles"></a> 

Azure NetApp Files 볼륨에는 *maxfiles* 라는 한도가 있습니다. Maxfiles 한도는 볼륨에 포함할 수 있는 파일 수입니다. Azure NetApp Files 볼륨의 maxfiles 한도는 볼륨의 크기(할당량)를 기준으로 인덱싱됩니다. 볼륨에 대한 maxfiles 한도는 프로비저닝된 볼륨 크기의 TiB당 2천만 개 파일의 비율로 증가하거나 감소합니다. 

서비스는 프로비저닝된 크기를 기준으로 볼륨의 maxfiles 한도를 동적으로 조정합니다. 예를 들어 처음에 1TiB 크기로 구성된 볼륨의 maxfiles 한도는 2천만 개입니다. 이후에 볼륨 크기를 변경하면 다음 규칙에 따라 maxfiles 한도가 자동으로 재조정됩니다. 

|    볼륨 크기(할당량)     |  maxfiles 한도의 자동 재조정    |
|----------------------------|-------------------|
|    <= 1TiB                |    2천만     |
|    > 1TiB <= 2TiB    |    4천만     |
|    > 2TiB <= 3TiB    |    6천만     |
|    > 3TiB <= 4TiB    |    8천만     |
|    > 4TiB                 |    1억    |

볼륨에 이미 4TiB 이상의 할당량을 할당한 경우 [지원 요청](#limit_increase)을 시작하여 maxfiles 한도를 1억 개 이상으로 늘릴 수 있습니다. 1억 개 파일(또는 그 일부)마다 해당 볼륨 할당량을 4TiB씩 늘려야 합니다.  예를 들어 maxfiles 한도를 1억 개에서 2억 개 파일(또는 그 사이에 있는 모든 수)로 늘리면 볼륨 할당량을 4TiB에서 8TiB로 늘려야 하는 것입니다.

## <a name="request-limit-increase"></a>요청 한도 증가 <a name="limit_increase"></a> 

Azure 지원 요청을 만들어 위의 표에서 조정 가능한 한도를 늘릴 수 있습니다. 

Azure Portal 탐색 화면에서: 

1. **도움말 + 지원** 을 클릭합니다.
2. **+ 새 지원 요청** 을 클릭합니다.
3. 기본 탭에서 다음 정보를 제공합니다. 
    1. 문제 유형: **서비스 및 구독 한도(할당량)** 를 선택합니다.
    2. 구독: 할당량을 늘려야 하는 리소스의 구독을 선택합니다.
    3. 할당량 유형: **스토리지: Azure NetApp Files 한도** 를 선택합니다.
    4. **다음: 솔루션** 을 클릭합니다.
4. 세부 정보 탭에서:
    1. 아래 표에서 해당 리소스 종류에 대한 다음 정보를 제공합니다.

        |  리소스  |    부모 리소스      |    요청된 새 제한     |    할당량 증가 이유       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  계정 |  *구독 ID*   |  요청된 새 최대 **계정** 수    |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |
        |  풀    |  구독 ID, NetApp 계정 URI  |  요청된 새 최대 **풀** 수   |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |
        |  볼륨  |  구독 ID, NetApp 계정 URI, 용량 풀 URI   |  요청된 새 최대 **볼륨** 수     |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |
        |  Maxfiles  |  구독 ID, NetApp 계정 URI, 용량 풀 URI, 볼륨 URI   |  요청된 새 최대 **maxfiles** 수     |  어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?  |    
        |  지역 간 복제 데이터 보호 볼륨  |  구독 ID, 대상 NetApp 계정 URI, 대상 용량 풀 URI, 원본 NetApp 계정 URI, 원본 용량 풀 URI, 원본 볼륨 URI   |  *요청된 새 최대 **지역 간 복제 데이터 보호 볼륨(대상 볼륨)** 수_     |  _어떤 시나리오 또는 사용 사례를 계기로 요청하게 되었나요?*  |    

    2. 적절한 지원 방법을 지정하고 계약 정보를 제공합니다.

    3. **다음: 검토 + 만들기** 를 클릭하여 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
