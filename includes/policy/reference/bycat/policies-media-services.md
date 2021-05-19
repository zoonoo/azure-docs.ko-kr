---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/04/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f6e4aaf143f077c40c6184fd8e55e437763ea2c6
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2021
ms.locfileid: "108762377"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[HTTPS 입력이 있는 Azure Media Services 작업은 입력 URI를 허용된 URI 패턴으로 제한해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe9914afe-31cd-4b8a-92fa-c887f847d477) |Media Services 작업에서 사용하는 HTTPS 입력을 알려진 엔드포인트로 제한합니다. 허용되는 작업 입력 패턴의 빈 목록을 설정하여 HTTPS 엔드포인트의 입력을 완전히 비활성화할 수 있습니다. 작업 입력에서 'baseUri'를 지정하는 경우 패턴은 이 값과 일치합니다. 'baseUri'가 설정되지 않은 경우 패턴은 'files' 속성과 일치합니다. |거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Media%20Services/Jobs_RestrictHttpInputs.json) |
