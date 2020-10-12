---
title: Azure 피어 링 서비스 온 보 딩 모델
description: Azure 피어 링 서비스를 등록 하는 방법에 대 한 자세한 정보
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "84871403"
---
# <a name="onboarding-peering-service-model"></a>온 보 딩 서비스 모델

피어 링 서비스의 온 보 딩 프로세스는 아래에 나열 된 두 개의 모델로 구성 됩니다.

 - 온 보 딩 서비스 연결

 - 온 보 딩 서비스 연결 원격 분석

위에 나열 된 모델에 대 한 작업 계획은 아래와 같이 설명 됩니다.

| **Step** | **동작**| **수신 항목**|
|-----------|---------|---------|---------|
|1|고객은 연결 파트너의 연결을 프로 비전 할 수 있습니다 (Microsoft와의 상호 작용 없음). |Microsoft에 연결 하 고 안정적이 고 신뢰할 수 있는 Microsoft 연결에 대 한 기술 요구 사항을 충족 하는 인터넷 공급자입니다.  |
|2 (선택 사항)|고객은 Azure Portal에 위치를 등록 합니다. 위치는 ISP/IXP 이름, 고객 사이트의 물리적 위치 (상태 수준), 서비스 공급자나 엔터프라이즈가 위치에 지정 된 IP 접두사에 의해 정의 됩니다.  |원격 분석: 인터넷 경로 모니터링, Microsoft에서 사용자의 가장 가까운 가장자리 POP 위치에 대 한 트래픽 우선 순위 지정. |



## <a name="onboarding-peering-service-connection"></a>온 보 딩 서비스 연결

피어 링 서비스 연결을 등록 하려면 다음을 수행 합니다.

ISP (인터넷 서비스 공급자) 또는 IX (Internet Exchange) 파트너와 함께 작업 하 여 네트워크를 Microsoft 네트워크에 연결 하는 피어 링 서비스를 가져옵니다.

[연결 공급자](location-partners.md) 가 피어 링 서비스에 대해 Microsoft와 파트너 관계를 유지 하는지 확인 합니다. 

## <a name="onboarding-peering-service-connection-telemetry"></a>온 보 딩 서비스 연결 원격 분석

고객은 Microsoft 네트워크에 액세스할 때 네트워크 대기 시간 및 성능을 모니터링 하기 위해 BGP 경로 분석과 같은 원격 분석을 선택할 수 있습니다. 이는 Azure Portal 연결을 등록 하 여 수행할 수 있습니다.

피어 링 서비스 연결 원격 분석을 등록 하려면 고객은 피어 링 서비스 연결을 Azure Portal에 등록 해야 합니다. 절차를 학습 하려면 [피어 링 서비스 등록-Azure Portal](azure-portal.md) 를 참조 하세요.

그 다음에는 [여기](measure-connection-telemetry.md)에서 참조 하 여 원격 분석을 측정할 수 있습니다.

## <a name="next-steps"></a>다음 단계

피어 링 서비스 연결을 등록 하는 방법에 대 한 단계별 프로세스를 알아보려면 [등록 피어 링 서비스-Azure Portal](azure-portal.md)를 참조 하세요.

연결 원격 분석 측정에 대 한 자세한 내용은 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조 하세요.
