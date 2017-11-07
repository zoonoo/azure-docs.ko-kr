---
title: "Azure 스택에서 공용 IP 주소 사용량을 볼 | Microsoft Docs"
description: "관리자는 지역에서 소비 하는 공용 IP 주소를 볼 수 있습니다."
services: azure-stack
documentationcenter: 
author: ScottNapolitan
manager: darmour
editor: 
ms.assetid: 0f77be49-eafe-4886-8c58-a17061e8120f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: scottnap
ms.openlocfilehash: 7651565eebf6272f307a4ce4790ca19b41bfa826
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2017
---
# <a name="view-public-ip-address-consumption-in-azure-stack"></a>Azure 스택에서 공용 IP 주소 사용량 보기

*적용 대상: Azure 스택 통합 시스템과 Azure 스택 개발 키트*

클라우드 관리자는 테 넌 트, 할당에 계속 사용할 수 있는 공용 IP 주소의 수와 그 위치에 할당 된 공용 IP 주소의 백분율에 할당 된 공용 IP 주소의 수를 볼 수 있습니다.

**공용 IP 풀 사용** 타일 테 넌 트 IaaS VM 인스턴스 패브릭 인프라에 대 한 사용 여부는 패브릭에서 모든 공용 IP 주소 풀에서 사용 되는 공용 IP 주소의 총 수를 표시 서비스 또는 명시적으로 테 넌 트에서 생성 된 공용 IP 주소 리소스입니다.

이 타일의 목적은 Azure 스택 관리자가이 위치에 사용 되는 공용 IP 주소의 전체 숫자의 의미를 부여 하는 것입니다. 이렇게 하면 있습니다 실행 중인지 확인 낮은이 리소스에서 관리자가 있습니다.

에 **리소스 공급자**, **네트워크** 블레이드에서 **공용 IP 주소** 아래에서 메뉴 항목 **테 넌 트 리소스** 해당 public만 나열 IP 주소는 *명시적으로 만든 테 넌 트에서*합니다. 따라서 수가 **사용** 에 공용 IP 주소는 **공용 IP 풀 사용** 타일은 항상 (보다 큰)와 다른에 숫자는 **공용 IP 주소** 바둑판식으로 배열 아래 **리소스를 테 넌 트**합니다.

## <a name="view-the-public-ip-address-usage-information"></a>공용 IP 주소 사용 정보 보기
지역에서 사용 되는 공용 IP 주소의 총 수를 보려면:

1. Azure 스택 관리자 포털에서 클릭 **더 많은 서비스**아래 **관리 리소스**, 클릭 **리소스 공급자**합니다.
2. 목록에서 **리소스 공급자**선택, **네트워크**합니다.
3. **네트워크** 블레이드를 표시 된 **공용 IP 풀 사용** 타일에 **개요** 섹션.

![네트워크 리소스 공급자 블레이드](media/azure-stack-viewing-public-ip-address-consumption/image01.png)

**사용** 숫자 모든 공용 IP 주소 풀에서 해당 위치에 할당 된 공용 IP 주소의 수를 나타냅니다. **무료** 번호가 나타내는 공용 IP 주소 수 모든 공용 IP에서 주소 할당 되지 않은 한 계속 사용할 수 있는 풀을 검색 합니다. **%Used** 사용 하거나 그 위치에 있는 모든 공용 IP 주소 풀에 공용 IP 주소의 총 수의 백분율 주소를 할당 된 숫자의 수를 나타냅니다.

## <a name="view-the-public-ip-addresses-that-were-created-by-tenant-subscriptions"></a>테 넌 트 구독에서 생성 된 공용 IP 주소를 확인 합니다.
에 특정 지역에서 테 넌 트 구독에서 명시적으로 생성 된 공용 IP 주소의 목록을 보려면 클릭 **공용 IP 주소** 아래 **테 넌 트 리소스**합니다.

![테 넌 트 공용 IP 주소](media/azure-stack-viewing-public-ip-address-consumption/image02.png)

동적으로 할당 된 일부 공용 IP 주소 목록에 나타나지만 아직 연결 된 주소를가지고 있지 않으면 나타날 수 있습니다. 즉, 주소 리소스가 아직 생성 되지 않았기 네트워크 리소스 공급자에는 있지만 네트워크 컨트롤러에 없습니다.

인터페이스, 네트워크 인터페이스 카드 (NIC), 부하 분산 장치 또는 가상 네트워크 게이트웨이에 바인딩되어 실제로 될 때까지 네트워크 컨트롤러가이 리소스에 주소를 할당 하지 않습니다. 공용 IP 주소는 인터페이스에 바인딩된 하 고, IP 주소를 할당 하는 네트워크 컨트롤러에 표시 하는 경우는 **주소** 필드입니다.

## <a name="view-the-public-ip-address-information-summary-table"></a>공용 IP 주소 정보 요약 테이블을 보려면
다른 사례 공용 IP 주소 할당 된 주소 하나의 목록에 표시 되는지 여부를 결정 하는 여러 가지가 있습니다.

| **공용 IP 주소 할당 사례** | **사용량 요약에 표시** | **테 넌 트 공용 IP 주소 목록에 표시** |
| --- | --- | --- |
| 아직 할당 되지 않은 NIC 또는 부하 분산 (임시)에 동적 공용 IP 주소 |아니요 |예 |
| 동적 공용 IP 주소는 NIC 또는 부하 분산 장치에 할당 합니다. |예 |예 |
| 고정 공용 IP 주소는 테 넌 트 NIC 또는 부하 분산 장치에 할당 합니다. |예 |예 |
| 고정 공용 IP 주소 인프라 서비스 패브릭 끝점에 할당 합니다. |예 |아니요 |
| 공용 IP 주소 IaaS VM 인스턴스에 대 한 암시적으로 만들고 가상 네트워크에서 아웃 바운드 NAT를 사용 합니다. Vm 인터넷 정보를 보낼 수 있도록 테 넌 트 VM 인스턴스를 만들 때마다 내부적 만들어집니다. |예 |아니요 |

## <a name="next-steps"></a>다음 단계
[Azure 스택에서 저장소 계정을 관리합니다](azure-stack-manage-storage-accounts.md)