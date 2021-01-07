---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c3c9e698aeddbe4818e7b77596eed81e60cb38e5
ms.sourcegitcommit: 9889a3983b88222c30275fd0cfe60807976fd65b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "95002165"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[공유 대시보드에 인라인 콘텐츠가 있는 markdown 타일이 없어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F04c655fe-0ac7-48ae-9a32-3a2e208c7624) |Markdown 타일에 인라인 콘텐츠를 포함 하는 공유 대시보드를 만들고 콘텐츠를 온라인으로 호스트 되는 markdown 파일로 저장 하는 것을 허용 하지 않습니다. Markdown 타일에서 인라인 콘텐츠를 사용 하는 경우 콘텐츠의 암호화를 관리할 수 없습니다. 사용자 고유의 저장소를 구성 하 여 암호화 하 고, 이중 암호화 하 고, 사용자 고유의 키를 가져올 수 있습니다. 이 정책을 사용 하도록 설정 하면 사용자가 2020-09-01-preview 이상 버전의 공유 대시보드 REST API를 사용 하도록 제한 됩니다. |감사, 거부, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Portal/SharedDashboardInlineContent_Deny.json) |
