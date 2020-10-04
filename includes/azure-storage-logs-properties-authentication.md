---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: e3732823be1c8391f2bec9018a094200c7c93a5e
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/04/2020
ms.locfileid: "91711410"
---
| 속성 | Description |
|:--- |:---|
|**identity / type** | 요청을 수행하는 데 사용된 인증 형식입니다. 예: `OAuth`, `SAS Key`, `Account Key` 또는 `Anonymous` |
|**identity / tokenHash**|이 필드는 내부 전용으로 예약되어 있습니다. |
|**authorization / action** | 요청에 할당된 작업입니다. |
|**authorization / roleAssignmentId** | 역할 할당 ID입니다. 예: `4e2521b7-13be-4363-aeda-111111111111`|
|**authorization / roleDefinitionId** | 역할 정의 ID입니다. 예: `ba92f5b4-2d11-453d-a403-111111111111"`|
|**principals / id** | 보안 주체의 ID입니다. 예: `a4711f3a-254f-4cfb-8a2d-111111111111`|
|**principals / type** | 보안 주체의 유형입니다. 예: `ServicePrincipal` |
|**requester / appID** | 요청자로 사용되는 OAuth(Open Authorization) 애플리케이션 ID입니다. <br> 예: `d3f7d5fe-e64a-4e4e-871d-333333333333`|
|**requester / audience** | 요청의 OAuth 대상 그룹입니다. 예: `https://storage.azure.com` |
|**requester / objectId** | 요청자의 OAuth 개체 ID입니다. Kerberos 인증의 경우 Kerberos로 인증된 사용자의 개체 식별자를 나타냅니다. 예: `0e0bf547-55e5-465c-91b7-2873712b249c` |
|**requester / tenantId** | ID의 OAuth 테넌트 ID입니다. 예: `72f988bf-86f1-41af-91ab-222222222222`|
|**requester / tokenIssuer** | OAuth 토큰 발급자입니다. 예: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`|
|**requester / upn** | 요청자의 UPN(사용자 계정 이름)입니다. 예: `someone@contoso.com` |
|**requester / userName** | 이 필드는 내부 전용으로 예약되어 있습니다.|