---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 10/07/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 468b2f73e07d48081c9ed67ca6720e57ac578570
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "91859532"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL 서버에 대해 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |고객이 관리 하는 키를 사용 하 여 미사용 데이터를 Azure Database for MySQL 데이터베이스 서버에 암호화 하는 경우 키 및 데이터 관리에서 의무 분리를 구현할 수 있습니다. 고객 관리 키를 구성 하는 경우 키를 사용 하 여 데이터를 암호화 하는 키에 대 한 액세스를 보호 하 고 제어 합니다. 회전 및 관리를 포함 하 여 주요 수명 주기에 대 한 모든 제어 및 책임이 있습니다. 규정 준수를 위해 고객 관리 키를 사용 해야 하는 경우도 있습니다. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. 이 구성은 데이터베이스 서버에 액세스할 수 있도록 항상 SSL을 사용 하도록 설정 합니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |Azure Database for MySQL를 사용 하 여 데이터베이스 서버에 대 한 중복성 옵션을 선택할 수 있습니다. 데이터가 서버에서 호스트 되는 지역 내에만 저장 되는 지역 중복 백업 저장소로 설정할 수 있으며 지역 장애 시 복구 옵션을 제공 하기 위해 쌍을 이루는 지역에도 복제 됩니다. 백업에 대 한 지역 중복 저장소 구성은 서버를 만드는 동안에만 허용 됩니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[MySQL 서버는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |가상 네트워크 기반 방화벽 규칙은 트래픽이 Azure 경계 내에 유지 되도록 하는 동시에 특정 서브넷의 트래픽을 Azure Database for MySQL 하는 데 사용 됩니다. 이 정책은 Azure Database for MySQL에 사용 되는 가상 네트워크 서비스 끝점이 있는지 감사 하는 방법을 제공 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |개인 끝점 연결은 Azure Database for MySQL에 대해 개인 연결을 사용 하도록 설정 하 여 보안 통신을 적용 합니다. 알려진 네트워크 에서만 들어오는 트래픽에 액세스할 수 있도록 개인 끝점 연결을 구성 하 고 Azure 내에서를 비롯 한 다른 모든 IP 주소의 액세스를 방지 합니다. |AuditIfNotExists, 사용 안 함 |[1.0.2](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |공용 네트워크 액세스 속성을 사용 하지 않도록 설정 하면 개인 끝점 에서만 Azure Database for MySQL에 액세스할 수 있도록 하 여 보안을 향상 시킵니다. 이 구성은 Azure IP 범위 외부에 있는 모든 공용 주소 공간에 대 한 액세스를 엄격히 사용 하지 않도록 설정 하 고 IP 또는 가상 네트워크 기반 방화벽 규칙과 일치 하는 모든 로그인을 거부 합니다. |감사, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
