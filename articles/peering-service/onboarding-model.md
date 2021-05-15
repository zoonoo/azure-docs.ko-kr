---
title: Azure Peering Service 온보딩 모델
description: Azure Peering Service를 온보드하는 방법에 대한 자세한 정보
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "84871403"
---
# <a name="onboarding-peering-service-model"></a>Peering Service 온보딩 모델

Peering Service의 온보딩 프로세스는 아래에 나열된 두 개의 모델로 구성됩니다.

 - Peering Service 연결 온보딩

 - Peering Service 연결 원격 분석 온보딩

위에 나열된 모델에 대한 작업 계획은 아래와 같이 설명됩니다.

| **Step** | **동작**| **수신 항목**|
|-----------|---------|---------|---------|
|1|고객이 연결 파트너를 통해 연결을 프로비전합니다(Microsoft와의 상호 작용 없음). |Microsoft에 올바르게 연결되어 있고, 성능 및 Microsoft과의 안정적인 연결에 대한 기술 요구 사항을 충족하는 인터넷 공급자입니다.  |
|2(선택 사항)|고객은 Azure Portal에 위치를 등록합니다. 위치는 ISP/IXP 이름, 고객 사이트의 물리적 위치(상태 수준), 서비스 공급자나 기업이 해당 위치에 지정한 IP 접두사로 정의됩니다.  |원격 분석: 인터넷 경로 모니터링, Microsoft에서 사용자의 가장 가까운 에지 POP 위치로의 트래픽 우선 순위 지정입니다. |



## <a name="onboarding-peering-service-connection"></a>Peering Service 연결 온보딩

Peering Service 연결을 온보드하려면 다음을 수행합니다.

ISP(인터넷 서비스 공급자) 또는 IX(인터넷 교환) 파트너와 협력하여 Peering Service를 얻어 네트워크를 Microsoft 네트워크와 연결합니다.

Peering Service를 위해 [연결 공급자](location-partners.md)가 Microsoft와 파트너 관계를 맺고 있는지 확인합니다. 

## <a name="onboarding-peering-service-connection-telemetry"></a>Peering Service 연결 원격 분석 온보딩

고객은 Microsoft 네트워크에 액세스할 때 BGP 경로 분석과 같은 원격 분석을 선택하여 네트워크 대기 시간 및 성능을 모니터링할 수 있습니다. Azure Portal에 연결을 등록하면 이 작업을 수행할 수 있습니다.

Peering Service 연결 원격 분석을 온보드하려면 고객이 Azure Portal에서 Peering Service 연결을 등록해야 합니다. 절차를 알아보려면 [Peering Service 등록 - Azure Portal](azure-portal.md)을 참조하세요.

그런 다음 [여기](measure-connection-telemetry.md)를 참조하여 원격 분석을 측정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

Peering Service 연결을 등록하는 방법에 대한 단계별 프로세스를 알아보려면 [Peering Service 등록 - Azure Portal](azure-portal.md)을 참조하세요.

원격 분석 측정에 대한 자세한 내용은 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.
