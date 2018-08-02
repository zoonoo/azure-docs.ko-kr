---
title: Azure Advisor 고가용성 권장 사항 | Microsoft Docs
description: Azure Advisor를 사용하여 Azure 배포의 가용성을 향상시킵니다.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 297a213fe4219b834187f977e3281eb939352f60
ms.sourcegitcommit: 156364c3363f651509a17d1d61cf8480aaf72d1a
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/25/2018
ms.locfileid: "39249066"
---
# <a name="advisor-high-availability-recommendations"></a>Advisor 고가용성 권장 사항

Azure Advisor는 업무상 중요한 응용 프로그램의 연속성을 보장하고 향상시키는 데 도움이 됩니다. Advisor 대시보드의 **고가용성** 탭에서 Advisor의 고가용성 권장 사항을 가져올 수 있습니다.

## <a name="ensure-virtual-machine-fault-tolerance"></a>가상 머신 내결함성 보장

응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 머신을 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 가용성 집합에 속하지 않는 가상 머신을 식별하고 이러한 가상 머신을 가용성 집합으로 이동할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 머신을 사용할 수 있고 Azure 가상 머신 SLA가 충족되도록 합니다. 가상 머신에 대한 가용성 집합을 만들거나 기존 가용성 집합에 가상 머신을 추가할 수도 있습니다.

> [!NOTE]
> 가용성 집합을 만들도록 선택한 경우 해당 가용성 집합에 적어도 하나 이상의 가상 컴퓨터를 추가해야 합니다. 중단 시간 동안 하나 이상의 컴퓨터를 사용할 수 있도록 하기 위해 가용성 집합에 둘 이상의 가상 머신을 그룹화하는 것이 좋습니다.

## <a name="ensure-availability-set-fault-tolerance"></a>가용성 집합 내결함성 보장 

응용 프로그램에 중복성을 제공하기 위해 여러 개의 가상 머신을 가용성 집합으로 그룹화하는 것이 좋습니다. Advisor는 단일 가상 머신을 포함하는 가용성 집합을 식별하고 여기에 하나 이상의 가상 머신을 추가할 것을 권장합니다. 이 구성은 계획된 유지 관리 또는 계획되지 않은 유지 관리 이벤트 중에 적어도 하나의 가상 머신을 사용할 수 있고 Azure 가상 머신 SLA가 충족되도록 합니다. 가상 머신을 만들거나 기존 가상 머신을 가용성 집합에 추가할 수 있습니다.  

## <a name="ensure-application-gateway-fault-tolerance"></a>응용 프로그램 게이트웨이 내결함성 보장
응용 프로그램 게이트웨이에서 구동되는 중요 업무용 응용 프로그램의 비즈니스 연속성을 위해 Advisor는 내결함성에 대해 구성되지 않은 응용 프로그램 게이트웨이 인스턴스를 식별하고 수행할 수 있는 수정 작업을 제안합니다. Advisor는 중간 규모 또는 대규모 단일 인스턴스 응용 프로그램 게이트웨이를 식별하고 하나 이상의 인스턴스 추가를 권장합니다. 또한 단일 또는 다중 인스턴스 소규모 응용 프로그램 게이트웨이를 식별하고 중간 규모 또는 대규모 SKU로 마이그레이션을 권장합니다. Advisor는 응용 프로그램 게이트웨이 인스턴스가 이러한 리소스에 대한 현재 SLA 요구 사항을 만족하도록 구성되어 있는지 확인하는 데 이러한 작업을 권장합니다.

## <a name="improve-the-performance-and-reliability-of-virtual-machine-disks"></a>가상 머신 디스크의 성능 및 안정성 향상

Advisor는 표준 디스크를 포함하는 가상 머신을 식별하고 프리미엄 디스크로 업그레이드할 것을 권장합니다.
 
Azure Premium Storage는 I/O 사용량이 많은 작업을 실행하는 가상 머신에서 대기 시간이 짧은 고성능 디스크 지원을 제공합니다. 프리미엄 저장소 계정을 사용하는 가상 머신 디스크는 SSD(솔리드 스테이트 드라이브)에 데이터를 저장합니다. 응용 프로그램이 최고 성능을 낼 수 있도록 높은 IOPS가 필요한 모든 가상 머신 디스크를 프리미엄 저장소로 마이그레이션하는 것이 좋습니다. 

디스크에 높은 IOPS가 필요하지 않으면 표준 저장소에 유지 관리하여 비용을 제한할 수 있습니다. 표준 저장소는 SSD 대신 HDD(하드 디스크 드라이브)에 가상 머신 디스크 데이터를 저장합니다. 가상 컴퓨터 디스크를 프리미엄 디스크로 마이그레이션하도록 선택할 수 있습니다. 프리미엄 디스크는 대부분의 가상 머신 SKU에서 지원됩니다. 그러나 일부 경우에 프리미엄 디스크를 사용하려는 경우 가상 머신 SKU도 업그레이드해야 할 수 있습니다.

## <a name="protect-your-virtual-machine-data-from-accidental-deletion"></a>가상 머신 데이터를 실수로 삭제되지 않도록 보호

가상 머신 백업 설정은 비즈니스에 중요한 데이터의 가용성을 보장하고 실수로 인한 삭제 또는 손상에 대한 보호를 제공합니다.  Advisor는 백업이 활성화되지 않은 가상 머신을 식별하고 백업을 활성화할 것을 권장합니다. 

## <a name="ensure-you-have-access-to-azure-cloud-experts-when-you-need-it"></a>필요할 때 Azure 클라우드 전문가에 대한 액세스 권한이 있는지 확인

중요 비즈니스용 워크로드를 실행할 때 필요한 경우 기술 지원에 대한 액세스 권한이 있어야 합니다. Advisor는 해당 지원 플랜에 포함된 기술 지원이 없는 잠재적 중요 비즈니스용 구독을 식별하고 기술 지원을 포함하는 옵션으로 업그레이드할 것을 권장합니다.

## <a name="create-azure-service-health-alerts-to-be-notified-when-azure-issues-affect-you"></a>Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고 만들기

Azure 문제가 영향을 미칠 때 알림을 받도록 Azure Service Health 경고를 만드는 것이 좋습니다. [Azure Service Health](https://azure.microsoft.com/features/service-health/)는 Azure 서비스 문제가 영향을 미칠 경우 개인화된 지침과 지원을 제공하는 무료 서비스입니다. Advisor는 경고가 구성되지 않은 구독을 식별하고 구독을 생성하도록 권장합니다.

## <a name="configure-traffic-manager-endpoints-for-resiliency"></a>복원력을 위해 Traffic Manager 엔드포인트 구성

여러 엔드포인트가 있는 Traffic Manager 프로필은 특정 엔드포인트가 실패할 경우 가용성이 더 높습니다. 서로 다른 지역에 엔드포인트를 배치하면 서비스 복원력을 더 높일 수 있습니다. Advisor는 엔드포인트가 하나뿐인 Traffic Manger 프로필을 식별하고 다른 지역에 하나 이상의 엔드포인트를 더 추가하도록 권장합니다.

근접 라우팅에 대해 구성된 Traffic Manger 프로필의 모든 엔드포인트가 같은 지역에 있는 경우 다른 지역의 사용자는 연결 지연을 경험할 수 있습니다. 엔드포인트를 다른 지역에 추가하거나 다른 지역으로 이동하면 한 지역의 모든 엔드포인트가 실패하는 경우 가용성을 더 높일 수 있습니다. Advisor는 근접 라우팅에 대해 구성되었으며 모든 엔드포인트가 같은 지역에 있는 Traffic Manager를 식별하고 다른 Azure 지역에 엔드포인트를 추가하거나 이동하도록 권장합니다.

Traffic Manager 프로필이 지리적 라우팅에 대해 구성된 경우 트래픽은 정의된 지역에 따라 엔드포인트로 라우팅됩니다. 한 지역이 실패하면 미리 정의된 장애 조치(failover)가 없습니다. 자역 그룹화가 “모두(세계)”로 구성된 엔드포인트가 있으면 트래픽 폐기를 방지하고 서비스 가용성을 높입니다. Advisor는 지역 그룹화가 “모두(세계)”로 구성된 엔드포인트가 없으며 지리적 라우팅 대상으로 구성된 Traffic Manager를 식별하고 구성을 변경하도록 권장합니다.

## <a name="how-to-access-high-availability-recommendations-in-advisor"></a>Advisor의 고가용성 권장 사항에 액세스하는 방법

1. [Azure Portal](https://portal.azure.com)에 로그인하고 [Advisor](https://aka.ms/azureadvisordashboard)를 엽니다.

2.  Advisor 대시보드에서 **고가용성** 탭을 클릭합니다.

## <a name="next-steps"></a>다음 단계

Advisor 권장 사항에 대한 자세한 내용은 다음을 참조하세요.
* [Azure Advisor 소개](advisor-overview.md)
* [Advisor 시작](advisor-get-started.md)
* [Advisor 비용 권장 사항](advisor-performance-recommendations.md)
* [Advisor 성능 권장 사항](advisor-performance-recommendations.md)
* [Advisor 보안 권장 사항](advisor-security-recommendations.md)

