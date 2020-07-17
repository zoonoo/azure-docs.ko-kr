---
title: Azure 피어 링 서비스 FAQ
description: Microsoft Azure 피어 링 서비스 Faq에 대해 알아보기
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 55c5e6c5b718dc2de295b9b4418ddc8607a69f8f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "84871363"
---
# <a name="peering-service-faq"></a>피어 링 서비스 FAQ

이 문서에서는 Azure 피어 링 서비스 연결에 대 한 가장 자주 묻는 질문을 설명 합니다.


**Q. 대상 고객은 누구 인가요?**

A. 대상 고객은 인터넷을 전송으로 사용 하 여 Microsoft 클라우드에 연결 하는 기업입니다.

**Q. 고객이 여러 공급자를 사용 하 여 피어 링 서비스에 등록할 수 있나요?** 

A. 예, 고객은 동일한 지역 또는 다른 지역에 있는 여러 공급자를 사용 하 여 피어 링 서비스에 등록할 수 있지만 동일한 접두사에 대해서는 등록할 수 없습니다.

**Q. 고객이 지역별로 사이트별로 고유한 ISP를 선택할 수 있나요?**

A. 예, 고객은이 작업을 수행할 수 있습니다. 비즈니스 및 운영 요구 사항에 적합 한 지역 당 파트너 ISP를 선택 합니다.

**Q. Microsoft Edge PoP 이란?**

A. Microsoft가 다른 네트워크와 상호 연결할 수 있는 실제 위치입니다. Microsoft Edge PoP 위치에서 Azure Front 도어 및 Azure CDN와 같은 서비스는 호스팅됩니다. 자세한 내용은 [Azure CDN](https://docs.microsoft.com/azure/cdn/cdn-features)을 참조하세요.

## <a name="peering-service-unique-characteristics"></a>피어 링 서비스: 고유 특성

**Q. 피어 링 서비스는 일반적인 인터넷 액세스와 어떻게 다르며 있나요?**

A. Microsoft 피어 링 서비스에 등록 된 파트너는 microsoft와 협력 하 여 Microsoft 서비스에 대 한 최적화 된 라우팅 및 안정적인 연결을 제공 합니다.  

**Q. 피어 링 서비스는 Express 경로와 어떻게 다르며**

A. Azure Express 경로는 하나 또는 여러 고객 위치에서의 전용 전용 연결입니다. 피어 링 서비스는 최적화 된 공용 연결을 제공 하 고 개인 연결을 지원 하지 않지만 로컬 인터넷 연결을 위해 최적화 된 연결을 제공 합니다.

## <a name="next-steps"></a>다음 단계

- 피어 링 서비스에 대해 알아보려면 [피어 링 서비스 개요](about.md)를 참조 하세요.
- 서비스 공급자를 찾으려면 [서비스 파트너 및 위치 피어 링](location-partners.md)을 참조 하세요.
- 피어 링 서비스 연결을 등록 하려면 [온 보 딩 서비스](onboarding-model.md)를 참조 하세요.
- 피어 링 서비스 연결을 등록 하려면 [피어 링 서비스 연결 등록-Azure Portal](azure-portal.md)을 참조 하세요.
- 원격 분석을 측정하려면 [연결 원격 분석 측정](measure-connection-telemetry.md)을 참조하세요.