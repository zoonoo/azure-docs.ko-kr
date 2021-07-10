---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 06/11/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 887d2784d4949a70763e6c08ce3ec72aaddd0bcd
ms.sourcegitcommit: 942a1c6df387438acbeb6d8ca50a831847ecc6dc
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/11/2021
ms.locfileid: "112019772"
---
|이름<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Media Services 계정은 Private Link를 지원하는 API를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fa77d8bb4-8d22-4bc1-a884-f582a705b480) |Azure Media Services 계정은 프라이빗 링크를 지원하는 API를 통해 만들어야 합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_RequirePrivateLinkSupport_Audit.json) |
|[레거시 v2 API에 대한 액세스를 허용하는 Azure Media Services 계정은 차단되어야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fccf93279-9c91-4143-a841-8d1f21505455) |Media Services 레거시 v2 API는 Azure Policy를 사용하여 관리할 수 없는 요청을 허용합니다. 2020-05-01 API 이상을 사용하여 만든 Media Services 리소스는 레거시 v2 API에 대한 액세스를 차단합니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/MediaServices_BlockRestV2_Audit.json) |
|[Azure Media Services 콘텐츠 키 정책은 토큰 인증을 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdaccf7e4-9808-470c-a848-1c5b582a1afb) |콘텐츠 키 정책은 콘텐츠 키에 액세스하기 위해 충족해야 하는 조건을 정의합니다. 토큰 제한을 사용하면 인증 서비스에서 유효한 토큰(예: Azure Active Directory)을 가진 사용자만 콘텐츠 키에 액세스할 수 있습니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/ContentKeyPolicies_RequireTokenAuth_Audit.json) |
|[HTTPS 입력이 있는 Azure Media Services 작업은 입력 URI를 허용된 URI 패턴으로 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9914afe-31cd-4b8a-92fa-c887f847d477) |Media Services 작업에서 사용하는 HTTPS 입력을 알려진 엔드포인트로 제한합니다. 허용되는 작업 입력 패턴의 빈 목록을 설정하여 HTTPS 엔드포인트의 입력을 완전히 비활성화할 수 있습니다. 작업 입력에서 'baseUri'를 지정하는 경우 패턴은 이 값과 일치합니다. 'baseUri'가 설정되지 않은 경우 패턴은 'files' 속성과 일치합니다. |거부, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/Jobs_RestrictHttpInputs.json) |
