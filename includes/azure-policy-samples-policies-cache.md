---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 1d5e916d9f8256c002f6810d1fc7aedebeba6481
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 02/12/2020
ms.locfileid: "77170011"
---
|속성 |Description |효과 |버전 |
|---|---|---|---|
|[Redis Cache에 보안 연결만 사용하도록 설정해야 함](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Cache/RedisCache_AuditSSLPort_Audit.json) |SSL을 통한 Redis Cache 연결의 사용만 감사합니다. 보안 연결을 사용하여 서버와 서비스 간 인증을 보장하고 전송 중인 데이터를 메시지 가로채기(man-in-the-middle), 도청 및 세션 하이재킹과 같은 네트워크 계층 공격으로부터 보호합니다. |감사, 거부, 사용 안 함 |1.0.0 |
