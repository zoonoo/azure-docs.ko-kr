---
title: Azure NetApp Files에 대한 리소스 제한 | Microsoft Docs
description: Azure NetApp 파일 리소스에 대한 제한 및 리소스 제한 증가를 요청하는 방법에 대해 설명합니다.
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
ms.date: 3/13/2020
ms.author: b-juche
ms.openlocfilehash: 36b2d50722a1840e461d6907f440d859c7c82117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/28/2020
ms.locfileid: "79408830"
---
# <a name="resource-limits-for-azure-netapp-files"></a>Azure NetApp Files에 대한 리소스 제한

Azure NetApp Files에 대한 리소스 제한을 이해하면 볼륨을 관리하는 데 도움이 됩니다.

## <a name="resource-limits"></a>리소스 한계

다음 표는 Azure NetApp 파일에 대한 리소스 제한을 설명합니다.

|  리소스  |  기본 제한  |  지원 요청을 통해 조정할 수 있습니다.  |
|----------------|---------------------|--------------------------------------|
|  Azure 지역당 NetApp 계정 수   |  10    |  yes   |
|  NetApp 계정당 용량 풀 수   |    25     |   yes   |
|  용량 풀당 볼륨 수     |    500   |    yes     |
|  볼륨당 스냅샷 수       |    255     |    예        |
|  Azure 가상 네트워크당 Azure NetApp 파일(Microsoft.NetApp/볼륨)에 위임된 서브넷 수    |   1   |    예    |
|  Azure NetApp 파일이 있는 VNet(즉시 피어온 VNet 포함)에서 사용된 IP 수   |    1000   |    yes   |
|  단일 용량 풀의 최소 크기   |  4TiB     |    예  |
|  단일 용량 풀의 최대 크기    |  500TiB   |   예   |
|  단일 볼륨의 최소 크기    |    100GiB    |    예    |
|  단일 볼륨의 최대 크기     |    100 TiB    |    예    |
|  단일 파일의 최대 크기     |    16TiB    |    예    |    
|  단일 디렉터리 최대 크기      |    320 MB    |    예    |    
|  볼륨당 최대 파일[수(최대 파일)](#maxfiles)     |    1억    |    yes    |    

자세한 내용은 [용량 관리 FAQ를](azure-netapp-files-faqs.md#capacity-management-faqs)참조하십시오.

## <a name="maxfiles-limits"></a>최대 파일 제한<a name="maxfiles"></a> 

Azure NetApp 파일 볼륨에는 *maxfiles*라는 제한이 있습니다. 최대 파일 제한은 볼륨에 포함할 수 있는 파일 수입니다. Azure NetApp 파일 볼륨의 최대 파일 제한은 볼륨의 크기(할당량)에 따라 인덱싱됩니다. 볼륨에 대한 최대 파일 제한은 프로비저닝된 볼륨 크기의 TiB당 2천만 개의 파일 비율로 증가하거나 감소합니다. 

서비스는 프로비저닝된 크기에 따라 볼륨의 최대 파일 제한을 동적으로 조정합니다. 예를 들어 처음에 1TiB 크기로 구성된 볼륨의 최대 파일 제한은 2천만 개입니다. 볼륨 크기를 계속 변경하면 다음 규칙에 따라 최대 파일 제한이 자동으로 재조정됩니다. 

|    볼륨 크기(할당량)     |  최대 파일 제한의 자동 재조정    |
|----------------------------|-------------------|
|    < 1 TiB                 |    2천만 명     |
|    >= 1 TiB하지만 < 2 TiB    |    4천만 명     |
|    >= 2 TiB 하지만 < 3 TiB    |    6천만     |
|    >= 3 TiB 하지만 < 4 TiB    |    8천만 명     |
|    >= 4 TiB                |    1억    |

모든 볼륨 크기의 경우 최대 파일 제한을 1억 개 이상으로 늘리는 [지원 요청을](#limit_increase) 시작할 수 있습니다.

## <a name="request-limit-increase"></a>요청 한도 증가<a name="limit_increase"></a> 

Azure 지원 요청을 만들어 위의 표에서 조정 가능한 제한을 늘릴 수 있습니다. 

Azure 포털 탐색 평면에서: 

1. **도움말 + 지원을**클릭합니다.
2. **+ 새 지원 요청을 클릭합니다.**
3. 기본 탭에서 다음 정보를 제공합니다. 
    1. 문제 유형: **서비스 및 구독 한도(할당량)를 선택합니다.**
    2. 구독: 할당량을 늘려야 하는 리소스에 대한 구독을 선택합니다.
    3. 할당량 유형: **저장소 선택: Azure NetApp 파일 제한.**
    4. **다음을 클릭합니다.**
4. 세부 정보 탭에서:
    1. 설명 상자에서 해당 리소스 유형에 대한 다음 정보를 제공합니다.

        |  리소스  |    상위 리소스      |    요청된 새 제한     |    할당량 증가 이유       |
        |----------------|------------------------------|---------------------------------|------------------------------------------|
        |  계정 |  *구독 ID*   |  *새 최대 **계정** 번호 요청*    |  *어떤 시나리오 또는 사용 사례가 요청에 대한 메시지를 표시합니까?*  |
        |  풀    |  *구독 ID, 계정 URI*  |  *요청된 새 최대 **풀** 번호*   |  *어떤 시나리오 또는 사용 사례가 요청에 대한 메시지를 표시합니까?*  |
        |  볼륨  |  *구독 ID, 계정 URI, 풀 URI*   |  *요청된 새 최대 **볼륨** 번호*     |  *어떤 시나리오 또는 사용 사례가 요청에 대한 메시지를 표시합니까?*  |
        |  최대 파일  |  *구독 ID, 계정 URI, 풀 URI, 볼륨 URI*   |  *요청된 새 최대 **최대 파일** 수*     |  *어떤 시나리오 또는 사용 사례가 요청에 대한 메시지를 표시합니까?*  |    

    2. 적절한 지원 방법을 지정하고 계약 정보를 제공합니다.

    3. **다음을 클릭: 검토 + 만들기로** 클릭하여 요청을 만듭니다. 


## <a name="next-steps"></a>다음 단계  

- [Azure NetApp Files의 스토리지 계층 구조 이해](azure-netapp-files-understand-storage-hierarchy.md)
- [Azure NetApp Files 비용 모델](azure-netapp-files-cost-model.md)
