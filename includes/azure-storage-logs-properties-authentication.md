---
author: normesta
ms.service: storage
ms.topic: include
ms.date: 09/28/2020
ms.author: normesta
ms.openlocfilehash: fc7157fad4227686ab4fddfe6ce3119119827c6b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2021
ms.locfileid: "121802189"
---
| 속성 | Description |
|:--- |:---|
|**identity / type** | 요청을 수행하는 데 사용된 인증 형식입니다. 예: `OAuth`, `Kerberos`, `SAS Key`, `Account Key` 또는 `Anonymous` |
|**identity / tokenHash**|요청에 사용되는 인증 토큰의 SHA-256 해시입니다. <br>인증 유형이 `Account Key`인 경우 형식은 "key1 \| key2(키의 SHA256 해시)"입니다. 예: `key1(5RTE343A6FEB12342672AFD40072B70D4A91BGH5CDF797EC56BF82B2C3635CE)`. <br>인증 유형이 `SAS Key`인 경우 형식은 "key1 \| key2(키의 SHA 256 해시), SasSignature(SAS 토큰의 SHA 256 해시)"입니다. 예: `key1(0A0XE8AADA354H19722ED12342443F0DC8FAF3E6GF8C8AD805DE6D563E0E5F8A),SasSignature(04D64C2B3A704145C9F1664F201123467A74D72DA72751A9137DDAA732FA03CF)`. 인증 형식이 `OAuth`인 경우 형식은 "OAuth 토큰의 SHA 256 해시"입니다. 예: `B3CC9D5C64B3351573D806751312317FE4E910877E7CBAFA9D95E0BE923DD25C`<br> 다른 인증 유형의 경우 tokenHash 필드가 없습니다. |
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
