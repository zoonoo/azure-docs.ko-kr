---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/14/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 7d9f08f3795d96a5bfc4a318dc90a4711be2e3ce
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/14/2021
ms.locfileid: "107512914"
---
|Name<br /><sub>(Azure Portal)</sub> |설명 |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성을 적용하면 데이터베이스 서버에 액세스할 때 항상 SSL을 사용하도록 설정됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
