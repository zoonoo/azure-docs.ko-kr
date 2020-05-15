---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/05/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 79acf34222087f6e62bc17377e61ec7be6613c41
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/05/2020
ms.locfileid: "82941230"
---
|속성 |Description |효과 |버전 |GitHub |
|---|---|---|---|---|
|[Redis Cache에 보안 연결만 사용하도록 설정해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F22bee202-a82f-4305-9a2a-6d7f44d4dedb) |SSL을 통한 Redis Cache 연결의 사용만 감사합니다. 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |
