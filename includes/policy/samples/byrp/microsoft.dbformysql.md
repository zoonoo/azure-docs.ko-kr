---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 6eca6936420c05098329455bcdf84b0c82afb865
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/11/2020
ms.locfileid: "86276681"
---
|Name<br /><sub>(Azure Portal)</sub> |Description |효과 |버전<br /><sub>(GitHub)</sub> |
|---|---|---|---|
|[MySQL 서버에 대해 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F83cef61d-dbd1-4b20-a4fc-5fbc7da10833) |이 정책은 BYOK(Bring Your Own Key) 데이터 보호를 사용하도록 설정하지 않은 환경의 MySQL 서버를 감사합니다. 자세한 내용은 [https://aka.ms/mysqlbyok](https://aka.ms/mysqlbyok)를 방문하세요. |AuditIfNotExists, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableByok_Audit.json) |
|[MySQL 데이터베이스 서버에 대해 SSL 연결 적용을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fe802a67a-daf5-4436-9ea6-f6d821dd0c5d) |이 정책은 SSL 연결을 적용하지 않는 모든 MySQL 서버를 감사합니다. MySQL용 Azure Database는 SSL(Secure Sockets Layer)을 사용한 MySQL용 Azure Database 서버와 클라이언트 애플리케이션 간 연결을 지원합니다. 데이터베이스 서버와 클라이언트 애플리케이션 간 SSL 연결을 적용하면 서버와 애플리케이션 간 데이터 스트림을 암호화함으로써 '메시지 가로채기(man in the middle)' 공격으로부터 보호할 수 있습니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnableSSL_Audit.json) |
|[Azure Database for MySQL에 대해 지역 중복 백업을 사용하도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82339799-d096-41ae-8538-b108becf0970) |이 정책은 지역 중복 백업이 사용하도록 설정되지 않은 모든 Azure Database for MySQL을 감사합니다. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/GeoRedundant_DBForMySQL_Audit.json) |
|[MySQL 서버는 가상 네트워크 서비스 엔드포인트를 사용해야 함](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F3375856c-3824-4e0e-ae6a-79e011dd4c47) |이 정책은 가상 네트워크 서비스 엔드포인트를 사용하도록 구성되지 않은 MySQL 서버를 감사합니다. 자세한 내용은 [https://aka.ms/mysqlvnet](https://aka.ms/mysqlvnet)을 방문하세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_VirtualNetworkServiceEndpoint_Audit.json) |
|[프라이빗 엔드포인트를 MySQL 서버에서 사용할 수 있어야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F7595c971-233d-4bcf-bd18-596129188c49) |이 정책은 프라이빗 엔드포인트를 사용하도록 구성되지 않은 모든 MySQL 서버를 감사하는 데 유용합니다. 자세한 내용은 [https://aka.ms/mysqlprivatelink](https://aka.ms/mysqlprivatelink)를 방문하세요. |AuditIfNotExists, 사용 안 함 |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_EnablePrivateEndPoint_Audit.json) |
|[MySQL 서버에 대해 공용 네트워크 액세스를 사용하지 않도록 설정해야 합니다.](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fd9844e8a-1437-4aeb-a32c-0c992f056095) |이 정책은 공용 네트워크 액세스를 사용하도록 설정하여 사용자 환경에서 MySQL 서버를 감사합니다. 자세한 내용은 [https://go.microsoft.com/fwlink/?linkid=2120014](https://go.microsoft.com/fwlink/?linkid=2120014)를 방문하세요. |감사, 사용 안 함 |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/SQL/MySQL_DisablePublicNetworkAccess_Audit.json) |
