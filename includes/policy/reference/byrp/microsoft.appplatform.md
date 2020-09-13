---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 09/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 3bbfc9d2045367d014ffc2c3cbc3f5696f9c1d6f
ms.sourcegitcommit: 43558caf1f3917f0c535ae0bf7ce7fe4723391f9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/11/2020
ms.locfileid: "90022332"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[분산 추적을 사용하지 않는 Azure Spring Cloud 인스턴스 감사](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0f2d8593-4667-4932-acca-6a9f187af109) |Azure Spring Cloud의 분산 추적 도구를 사용하면 애플리케이션에서 마이크로서비스 간의 복잡한 상호 연결을 디버그하고 모니터링할 수 있습니다. 분산 추적 도구는 사용하도록 설정되고 정상 상태여야 합니다. |감사, 사용 안 함 |[1.0.0 - 미리 보기](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_DistributedTracing_Audit.json) |
|[Azure 스프링 클라우드는 네트워크 주입을 사용 해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Faf35e2a4-ef96-44e7-a9ae-853dd97032c4) |Azure 스프링 클라우드 인스턴스는 다음 용도로 가상 네트워크 주입을 사용 해야 합니다. 1. Azure 스프링 클라우드를 인터넷에서 격리 합니다. 2. Azure 스프링 클라우드를 사용 하 여 온-프레미스 데이터 센터 또는 다른 가상 네트워크의 Azure 서비스에서 시스템을 조작할 수 있습니다. 3. 고객이 Azure 스프링 클라우드의 인바운드 및 아웃 바운드 네트워크 통신을 제어할 수 있습니다. |감사, 사용 안 함, 거부 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/App%20Platform/Spring_VNETEnabled_Audit.json) |
