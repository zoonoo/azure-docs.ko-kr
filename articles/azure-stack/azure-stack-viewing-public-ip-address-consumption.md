---
title: Azure Stack에서 공용 IP 주소 사용 보기 | Microsoft Docs
description: 관리자는 지역에 공용 IP 주소 사용을 확인할 수 있습니다.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d97674940f0af91bf50af87cfe96fda9644d469b
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242055"
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Azure Stack에서 공용 IP 주소 사용 보기

*적용 대상: Azure Stack 통합 시스템 및 Azure Stack 개발 키트*

클라우드 관리자는 다음을 볼 수 있습니다.
 - 테 넌 트에 할당 된 공용 IP 주소의 수입니다.
 - 할당에 대 한 계속 사용할 수 있는 공용 IP 주소의 수입니다.
 - 해당 위치에 할당 된 공용 IP 주소의 백분율입니다.

합니다 **공용 IP 풀 사용량** 타일에 공용 IP 주소 풀에서 사용 하는 공용 IP 주소 수가 표시 됩니다. 각 IP 주소에 대 한 타일에는 인스턴스, 패브릭 인프라 서비스 및 테 넌 트에서 명시적으로 생성 된 공용 IP 주소 리소스 테 넌 트 IaaS VM에 대 한 사용량을 표시 합니다.

타일의 목적은 Azure Stack 운영자를이 위치에 사용 된 공용 IP 주소 수가 의미 있도록 합니다. 수는 있는지 여부를 실행 중인 낮은이 리소스를 확인 하는 관리자 수 있습니다.

합니다 **공용 IP 주소** 아래에서 메뉴 항목 **테 넌 트 리소스** 만 해당 공용 IP 주소 나열 된 *명시적으로 만든 테 넌 트에서*합니다. 메뉴 항목을 찾을 수 있습니다는 **리소스 공급자**하십시오 **네트워크** 창입니다. 수가 **사용** 에서 공용 IP 주소를 **공용 IP 풀 사용량** 타일 (보다 더 큼)와 항상 다릅니다.에서 숫자를 **공용 IP 주소** 타일 **리소스를 테 넌 트**합니다.

## <a name="view-the-public-ip-address-usage-information"></a>공용 IP 주소 사용 정보를 보려면

총 지역에 소비한 공용 IP 주소를 보려면:

1. Azure Stack 관리자 포털에서 선택 **모든 서비스**합니다. 그런 다음 합니다 **관리** 범주 선택 **네트워크**합니다.
1. **네트워크** 창에 표시 됩니다는 **공용 IP 풀 사용** 타일에 **개요** 섹션입니다.

![네트워크 리소스 공급자 창](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

합니다 **사용** 번호는 공용 IP 주소 풀에서 할당 된 공용 IP 주소 수를 나타냅니다. 합니다 **무료** 주소 할당 되지 않은 한 계속 사용할 수 있는 풀 번호에서 공용 IP 주소를 공용 IP의 수를 나타냅니다. 합니다 **% 사용** 사용 하거나 해당 위치에 공용 IP 주소 풀에서 공용 IP 주소의 총 비율로 할당 된 주소가 수 수를 나타냅니다.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>테 넌 트 구독에 의해 생성 된 공용 IP 주소를 보려면

선택 **공용 IP 주소** 아래에서 **테 넌 트 리소스**합니다. 특정 지역의 테 넌 트 구독을 명시적으로 만든 공용 IP 주소의 목록을 검토 합니다.

![테 넌 트 공용 IP 주소](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

동적으로 할당 된 일부 공용 IP 주소 목록에 표시 되는지 확인할 수 있습니다. 그러나 주소 아직 상호 연결 되었습니다 되지 않았습니다. 네트워크 리소스 공급자에는 있지만 아직 네트워크 컨트롤러에는 주소 리소스를 만들었습니다.

네트워크 컨트롤러 인터페이스, 네트워크 인터페이스 카드 (NIC), 부하 분산 장치 또는 가상 네트워크 게이트웨이 바인딩할 때까지 리소스에 주소를 할당 하지 않습니다. 공용 IP 주소를 인터페이스에 바인딩할 때 네트워크 컨트롤러 IP 주소를 할당 합니다. 주소에 표시 된 **주소** 필드입니다.

## <a name="view-the-public-ip-address-information-summary-table"></a>공용 IP 주소 정보 요약 테이블을 보려면

다른 경우에 공용 ip 주소를 하나의 목록에 표시할지 여부를 결정 하는 할당 됩니다.

| **공용 IP 주소 할당 사례** | **사용량 요약에 표시 됩니다.** | **테 넌 트 공용 IP 주소 목록에 표시 됩니다.** |
| --- | --- | --- |
| 동적 공용 IP 주소 (임시) NIC 또는 부하 분산 장치에 할당 되지 않음 |아닙니다. |예 |
| 동적 공용 IP 주소를 NIC 또는 부하 분산 장치에 할당 합니다. |예 |예 |
| 고정 공용 IP 주소는 테 넌 트 NIC 또는 부하 분산 장치에 할당 합니다. |예 |예 |
| 고정 공용 IP 주소는 패브릭 인프라 서비스 끝점에 할당 합니다. |예 |아닙니다. |
| 공용 IP 주소 IaaS VM 인스턴스에 대 한 암시적으로 만들고 가상 네트워크에서 아웃 바운드 NAT에 대 한 사용 합니다. 이러한 값은 테 넌 트 Vm을 인터넷에 정보를 보낼 수 있도록 VM 인스턴스를 만들 때마다 백그라운드에서 생성 됩니다. |예 |아닙니다. |

## <a name="next-steps"></a>다음 단계

[Azure Stack의 Storage 계정 관리](azure-stack-manage-storage-accounts.md)