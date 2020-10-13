---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: c4619610cc8e54f9fbe556e3d12668a680a2e261
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859459"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[Azure Database for MariaDB에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0ec47710-77ff-4a3d-9181-6aa50af424d0) |Azure Database for MariaDB를 사용 하 여 데이터베이스 서버에 대 한 중복성 옵션을 선택할 수 있습니다. 데이터가 서버에서 호스트 되는 지역 내에만 저장 되는 지역 중복 백업 저장소로 설정할 수 있으며 지역 장애 시 복구 옵션을 제공 하기 위해 쌍을 이루는 지역에도 복제 됩니다. 백업에 대 한 지역 중복 저장소 구성은 서버를 만드는 동안에만 허용 됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMariaDB_Audit.json) |
|[MariaDB 서버는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdfbd9a64-6114-48de-a47d-90574dc2e489) |가상 네트워크 기반 방화벽 규칙은 트래픽이 Azure 경계 내에 유지 되도록 하는 동시에 특정 서브넷의 트래픽을 Azure Database for MariaDB 하는 데 사용 됩니다. 이 정책은 Azure Database for MariaDB에 사용 되는 가상 네트워크 서비스 끝점이 있는지 감사 하는 방법을 제공 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_VirtualNetworkServiceEndpoint_Audit.json) |
|[프라이빗 엔드포인트를 MariaDB 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F0a1302fb-a631-4106-9753-f3d494733990) |개인 끝점 연결은 Azure Database for MariaDB에 대해 개인 연결을 사용 하도록 설정 하 여 보안 통신을 적용 합니다. 알려진 네트워크 에서만 들어오는 트래픽에 액세스할 수 있도록 개인 끝점 연결을 구성 하 고 Azure 내에서를 비롯 한 다른 모든 IP 주소의 액세스를 방지 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_EnablePrivateEndPoint_Audit.json) |
|[MariaDB 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ffdccbe47-f3e3-4213-ad5d-ea459b2fa077) |공용 네트워크 액세스 속성을 사용 하지 않도록 설정 하면 개인 끝점 에서만 Azure Database for MariaDB에 액세스할 수 있도록 하 여 보안을 향상 시킵니다. 이 구성은 Azure IP 범위 외부에 있는 모든 공용 주소 공간에 대 한 액세스를 엄격히 사용 하지 않도록 설정 하 고 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치 하는 모든 로그인을 거부 합니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MariaDB_DisablePublicNetworkAccess_Audit.json) |
