---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 05/29/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 83b8e9d2bd65d04a5260495c5add3cb98288955c
ms.sourcegitcommit: f1132db5c8ad5a0f2193d751e341e1cd31989854
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/31/2020
ms.locfileid: "84235467"
---
|속성 |Description |효과 |버전 |GitHub |
|---|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |이 정책은 지역 중복 백업이 사용하도록 설정되지 않은 모든 Azure Database for MariaDB를 감사합니다. |감사, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 서버는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 MariaDB 서버를 감사합니다. 자세한 내용은 [https://aka.ms/mariadbvirtualnetwork](https://aka.ms/mariadbvirtualnetwork)를 방문하세요. |AuditIfNotExists, 사용 안 함 |1.0.1 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |이 정책은 프라이빗 엔드포인트를 사용하도록 구성되지 않은 모든 MariaDB 서버를 감사하는 데 유용합니다. 자세한 내용은 [https://aka.ms/mariadbprivatelink](https://aka.ms/mariadbprivatelink)를 방문하세요. |AuditIfNotExists, 사용 안 함 |1.0.1 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |이 정책은 공용 네트워크 액세스를 사용하도록 설정하여 사용자 환경에서의 MariaDB 서버를 감사합니다. 자세한 내용은 [https://go.microsoft.com/fwlink/?linkid=2119542](https://go.microsoft.com/fwlink/?linkid=2119542)를 방문하세요. |감사, 사용 안 함 |1.0.0 |[링크](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
