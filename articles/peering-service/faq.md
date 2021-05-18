---
title: Azure Peering Service FAQ
description: Microsoft Azure Peering Service FAQ에 대해 알아보기
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 95ce90dbbf47ffe527fe6f25704d9cd28b834ea9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 03/29/2021
ms.locfileid: "95026698"
---
# <a name="peering-service-faq"></a>Peering Service FAQ

이 문서에서는 Azure Peering Service 연결에 대한 질문과 대답을 설명합니다.


**질문. 대상 고객은 누구인가요?**

A. 대상 고객은 인터넷을 전송으로 사용하여 Microsoft 클라우드에 연결하는 기업입니다.

**질문. 고객이 여러 공급자를 사용하여 Peering Service에 등록할 수 있나요?** 

A. 예, 고객은 동일한 지역 또는 다른 지역에 있는 여러 공급자를 사용하여 Peering Service에 등록할 수 있지만, 동일한 접두사는 등록할 수 없습니다.

**질문. 고객이 지역별로 자신의 사이트에 고유한 ISP를 선택할 수 있나요?**

A. 예, 고객은 이 작업을 수행할 수 있습니다. 비즈니스 및 운영 요구 사항에 적합한 지역별 파트너 ISP를 선택합니다.

**질문. Microsoft Edge PoP란?**

A. Microsoft가 다른 네트워크와 상호 연결되는 실제 위치입니다. Microsoft Edge PoP 위치에서 Azure Front Door 및 Azure CDN과 같은 서비스가 호스트됩니다. 자세한 내용은 [Azure CDN](../cdn/cdn-features.md)을 참조하세요.

## <a name="peering-service-unique-characteristics"></a>Peering Service: 고유 특성

**질문. Peering Service는 일반적인 인터넷 액세스와 어떻게 다른가요?**

A. Microsoft Peering Service에 등록된 파트너는 Microsoft와 공동으로 Microsoft 서비스에 최적화된 라우팅 및 안정적인 연결을 제공합니다.  

**질문. Peering Service는 ExpressRoute와 어떻게 다른가요?**

A. Azure ExpressRoute는 하나 또는 여러 고객 위치에서의 프라이빗 전용 연결입니다. Peering Service는 최적화된 공용 연결을 제공하지만 개인 연결은 지원하지 않는 반면, 로컬 인터넷 전환을 위해 최적화된 연결을 제공합니다.

## <a name="next-steps"></a>다음 단계

- Peering Service에 대해 알아보려면 [Peering Service 개요](about.md)를 참조하세요.
- 서비스 공급자를 찾으려면 [Peering Service 파트너 및 위치](location-partners.md)를 참조하세요.
- Peering Service 연결을 온보딩하려면 [Peering Service 온보딩](onboarding-model.md)을 참조하세요.
- 연결을 등록하는 방법에 대한 자세한 내용은 [Peering Service 연결 등록 - Azure Portal](azure-portal.md)을 참조하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.