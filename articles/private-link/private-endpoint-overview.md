---
title: Azure 개인 엔드포인트란?
description: Azure 개인 엔드포인트에 대해 알아보기
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: conceptual
ms.date: 01/09/2020
ms.author: allensu
ms.openlocfilehash: 6782d745bfced576fe06019b0d41af86c8c63ed4
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656265"
---
# <a name="what-is-azure-private-endpoint"></a>Azure 개인 엔드포인트란?

Azure 프라이빗 엔드포인트는 Azure Private Link가 제공하는, 서비스에 비공개로 안전하게 연결하는 네트워크 인터페이스입니다. 프라이빗 엔드포인트는 VNet의 개인 IP 주소를 사용하여 서비스를 VNet으로 효과적으로 가져오게 됩니다. 서비스는 Azure 저장소, Azure Cosmos DB, SQL 등과 같은 Azure 서비스 또는 사용자 고유의 [개인 링크 서비스일](private-link-service-overview.md)수 있습니다.
  
## <a name="private-endpoint-properties"></a>프라이빗 엔드포인트 속성 
 전용 끝점은 다음 속성을 지정합니다. 


|속성  |설명 |
|---------|---------|
|이름    |    리소스 그룹 내의 고유한 이름입니다.      |
|서브넷    |  가상 네트워크에서 개인 IP 주소를 배포하고 할당하는 서브넷입니다. 서브넷 요구 사항은 이 문서의 제한 섹션을 참조하십시오.         |
|개인 링크 리소스    |   사용 가능한 형식 목록에서 리소스 ID 또는 별칭을 사용하여 연결할 개인 링크 리소스입니다. 이 리소스로 전송되는 모든 트래픽에 대해 고유한 네트워크 식별자가 생성됩니다.       |
|대상 하위 리소스   |      연결할 하위 리소스입니다. 각 개인 링크 리소스 유형에는 기본 설정에 따라 선택할 수 있는 옵션이 다릅니다.    |
|연결 승인 방법    |  자동 또는 수동. RBAC(역할 기반 액세스 제어) 권한에 따라 개인 엔드포인트를 자동으로 승인할 수 있습니다. RBAC 없이 개인 링크 리소스에 연결 하려고 하는 경우 수동 메서드를 사용 하 여 리소스의 소유자가 연결을 승인 할 수 있도록 합니다.        |
|요청 메시지     |  요청된 연결이 수동으로 승인될 메시지를 지정할 수 있습니다. 이 메시지는 특정 요청을 식별하는 데 사용할 수 있습니다.        |
|연결 상태   |   전용 끝점이 활성 상태인지 지정하는 읽기 전용 속성입니다. 승인된 상태의 개인 끝점만 트래픽을 전송하는 데 사용할 수 있습니다. 사용 가능한 추가 상태: <br>-**승인됨**: 연결이 자동으로 또는 수동으로 승인되었으며 사용할 준비가 되었습니다.</br><br>-**보류 중**: 연결이 수동으로 만들어졌으며 개인 링크 리소스 소유자의 승인 대기 중입니다.</br><br>-**거부됨**: 개인 링크 리소스 소유자가 연결을 거부했습니다.</br><br>-**연결 끊김**: 개인 링크 리소스 소유자가 연결을 제거했습니다. 개인 끝점은 정보를 제공하며 정리를 위해 삭제해야 합니다. </br>|

다음은 개인 끝점에 대한 몇 가지 주요 세부 정보입니다. 
- 프라이빗 엔드포인트를 사용하면 동일한 VNet, 지역적으로 피어링된 VNet, 전 세계적으로 피어링된 VNet 및 사설 링크로 구동되는 [VPN](https://azure.microsoft.com/services/vpn-gateway/) 또는 [익스프레스 경로](https://azure.microsoft.com/services/expressroute/) 및 서비스를 사용하여 온프레미스에서 소비자 간의 연결을 가능하게 합니다.
 
- 개인 끝점을 만들 때 리소스의 수명 주기에 대해서도 네트워크 인터페이스가 만들어집니다. 인터페이스는 개인 링크 서비스에 매핑되는 서브넷에서 개인 IP 주소를 할당합니다.
 
- 프라이빗 엔드포인트는 가상 네트워크와 동일한 지역에 배포되어야 합니다. 
 
- 개인 링크 리소스는 가상 네트워크 및 개인 끝점이 아닌 다른 지역에 배포할 수 있습니다.
 
- 동일한 개인 링크 리소스를 사용하여 여러 개인 끝점을 만들 수 있습니다. 일반적인 DNS 서버 구성을 사용하는 단일 네트워크의 경우 DNS 확인에서 중복 항목이나 충돌을 방지하기 위해 지정된 개인 링크 리소스에 대해 단일 개인 끝점을 사용하는 것이 좋습니다. 
 
- 동일한 가상 네트워크 내에서 동일하거나 다른 서브넷에서 여러 개의 개인 엔드포인트를 만들 수 있습니다. 구독에서 만들 수 있는 개인 끝점 수에는 제한이 있습니다. 자세한 내용은 [Azure 제한을](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#networking-limits)참조하십시오.


 
## <a name="private-link-resource"></a>개인 링크 리소스 
개인 링크 리소스는 지정된 개인 끝점의 대상 대상입니다. 다음은 사용 가능한 개인 링크 리소스 유형 목록입니다. 
 
|개인 링크 리소스 이름  |리소스 유형   |하위 리소스  |
|---------|---------|---------|
|**프라이빗 링크** 서비스(나만의 서비스)   |  마이크로소프트.네트워크/개인링크서비스       | empty |
|**Azure SQL Database** | Microsoft.Sql/servers    |  SQL 서버 (sqlServer)        |
|**Azure Synapse Analytics** | Microsoft.Sql/servers    |  SQL 서버 (sqlServer)        | 
|**Azure Storage**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> 표(표, table_secondary)<BR> 큐(큐, queue_secondary)<BR> 파일(파일, file_secondary)<BR> 웹(웹, web_secondary)        |
|**Azure Data Lake Storage Gen2**  | Microsoft.Storage/storageAccounts    |  Blob (blob, blob_secondary)<BR> 데이터 레이크 파일 시스템 Gen2(dfs, dfs_secondary)       |
|**Azure Cosmos DB** | 마이크로소프트.Azure코스모스DB/데이터베이스계정 | Sql, 몽고DB, 카산드라, 그렘린, 테이블|
|**PostgreSQL -단일 서버에 대한 Azure 데이터베이스** | Microsoft.DBforPostgreSQL/servers   | 포스트그레SQL서버 |
|**Azure Database for MySQL** | Microsoft.DBforMySQL/servers    | 마이sql서버 |
|**Azure Database for MariaDB** | Microsoft.DBforMariaDB/servers    | 마리아드서버 |
|**Azure 키 볼트** | Microsoft.KeyVault/vaults    | 자격 증명 모음 |
|**Azure Kubernetes 서비스 - 쿠베네츠 API** | Microsoft.ContainerService/managedClusters | 관리되는 클러스터 |
|**Azure Search** | 마이크로소프트.검색/검색 서비스| 검색 서비스|  
|**Azure Container Registry** | Microsoft.ContainerRegistry/registries  | 사용된 |
|**Azure App Configuration** | 마이크로소프트.앱구성/구성스토어   | 구성 스토어 |
|**Azure Backup** | Microsoft.RecoveryServices/vaults   | 자격 증명 모음 |
|**Azure 이벤트 허브** | Microsoft.EventHub/namespaces    | namespace |
|**Azure Service Bus** | Microsoft.ServiceBus/namespaces | namespace |
|**Azure Relay** | Microsoft.Relay/namespaces | namespace |
|**Azure Event Grid** | Microsoft.EventGrid/topics  | 토픽 |
|**Azure Event Grid** | 마이크로소프트.이벤트 그리드/도메인 | 도메인 |
|**Azure 웹앱** | Microsoft.Web/sites    | site |
|**Azure 기계 학습** | Microsoft.MachineLearningServices/workspaces  | 작업 영역 |
  
 
## <a name="network-security-of-private-endpoints"></a>개인 엔드포인트의 네트워크 보안 
Azure 서비스에 개인 끝점을 사용하는 경우 트래픽은 특정 개인 링크 리소스에 보호됩니다. 플랫폼은 액세스 제어를 수행하여 지정된 개인 링크 리소스에만 도달하는 네트워크 연결의 유효성을 검사합니다. 동일한 Azure 서비스 내에서 추가 리소스에 액세스하려면 추가 개인 끝점이 필요합니다. 
 
공용 끝점에 액세스하여 지원되는 Azure 서비스에 연결하지 못하도록 워크로드를 완전히 잠글 수 있습니다. 이 컨트롤은 동일한 Azure 서비스에서 호스팅되는 다른 리소스에 대한 액세스를 방지하는 기본 제공 유출 보호를 제공하여 리소스에 추가 네트워크 보안 계층을 제공합니다. 
 
## <a name="access-to-a-private-link-resource-using-approval-workflow"></a>승인 워크플로를 사용하여 개인 링크 리소스에 액세스 
다음 연결 승인 방법을 사용하여 개인 링크 리소스에 연결할 수 있습니다.
- 특정 개인 링크 리소스를 소유하거나 권한이 있는 경우 **자동으로** 승인됩니다. 필요한 권한은 다음 형식의 개인 링크 리소스 유형을 기반으로 합니다. \<공급자>/<resource_type>/개인Endpoint연결승인/작업
- 필요한 권한이 없고 액세스를 요청하려는 경우 **수동** 요청입니다. 승인 워크플로가 시작됩니다. 프라이빗 엔드포인트 및 후속 프라이빗 엔드포인트 연결은 "보류 중" 상태로 만들어집니다. Private Link 리소스 소유자가 연결을 승인해야 합니다. 승인되면 다음 승인 워크플로다이어그램과 같이 개인 끝점이 정상적으로 트래픽을 보낼 수 있습니다.  

![워크플로 승인](media/private-endpoint-overview/private-link-paas-workflow.png)
 
개인 링크 리소스 소유자는 개인 끝점 연결을 통해 다음 작업을 수행할 수 있습니다. 
- 모든 개인 끝점 연결 세부 정보를 검토합니다. 
- 개인 끝점 연결을 승인합니다. 해당 개인 끝점이 개인 링크 리소스로 트래픽을 보낼 수 있습니다. 
- 개인 끝점 연결을 거부합니다. 해당 개인 끝점이 상태를 반영하도록 업데이트됩니다.
- 모든 상태에서 개인 끝점 연결을 삭제합니다. 해당 개인 끝점은 작업을 반영하도록 연결이 끊어진 상태로 업데이트되며, 개인 끝점 소유자는 이 시점에서만 리소스를 삭제할 수 있습니다. 
 
> [!NOTE]
> 승인된 상태의 개인 끝점만 지정된 개인 링크 리소스로 트래픽을 보낼 수 있습니다. 

### <a name="connecting-using-alias"></a>별칭을 사용하여 연결
별칭은 서비스 소유자가 표준 로드 밸러터 뒤에 개인 링크 서비스를 만들 때 생성되는 고유한 모니커입니다. 서비스 소유자는 이 별칭을 오프라인으로 소비자와 공유할 수 있습니다. 소비자는 리소스 URI 또는 별칭을 사용하여 개인 링크 서비스에 대한 연결을 요청할 수 있습니다. 별칭을 사용하여 연결하려면 수동 연결 승인 방법을 사용하여 개인 끝점을 만들어야 합니다. 수동 연결 승인 방법을 사용하는 경우 개인 끝점 작성 흐름 중에 수동 요청 매개 변수를 true로 설정합니다. 자세한 내용은 [New-AzPrivateEndpoint](/powershell/module/az.network/new-azprivateendpoint?view=azps-2.6.0) 및 [az 네트워크 프라이빗 엔드포인트 생성을](/cli/azure/network/private-endpoint?view=azure-cli-latest#az-network-private-endpoint-create) 확인합니다. 

## <a name="dns-configuration"></a>DNS 구성 
연결 문자열의 일부로 정규화된 도메인 이름(FQDN)을 사용하여 개인 링크 리소스에 연결할 때 할당된 개인 IP 주소로 확인하도록 DNS 설정을 올바르게 구성하는 것이 중요합니다. 기존 Azure 서비스에공용 끝점을 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. 개인 끝점을 사용하여 연결하려면 이 것을 재정의해야 합니다. 
 
개인 끝점과 연결된 네트워크 인터페이스에는 지정된 개인 링크 리소스에 할당된 FQDN 및 개인 IP 주소를 포함하여 DNS를 구성하는 데 필요한 전체 정보 집합이 포함되어 있습니다. 
 
다음 옵션을 사용하여 개인 끝점에 대한 DNS 설정을 구성할 수 있습니다. 
- **호스트 파일(테스트용으로만 권장)을 사용합니다.** 가상 컴퓨터에서 호스트 파일을 사용하여 DNS를 재정의할 수 있습니다.  
- **개인 DNS 영역을 사용합니다.** 개인 DNS 영역을 사용하여 지정된 개인 끝점에 대한 DNS 해상도를 재정의할 수 있습니다. 개인 DNS 영역을 가상 네트워크에 연결하여 특정 도메인을 확인할 수 있습니다.
- **사용자 지정 DNS 서버를 사용합니다.** 사용자 고유의 DNS 서버를 사용하여 지정된 개인 링크 리소스에 대한 DNS 해상도를 재정의할 수 있습니다. [DNS 서버가](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 가상 네트워크에서 호스팅되는 경우 DNS 전달 규칙을 만들어 개인 DNS 영역을 사용하여 모든 개인 링크 리소스에 대한 구성을 단순화할 수 있습니다.
 
> [!IMPORTANT]
> 공용 끝점을 해결하기 위해 적극적으로 사용 중인 영역을 재정의하지 않는 것이 좋습니다. DNS가 공용 DNS로 전달되지 않으면 리소스에 대한 연결이 올바르게 해결되지 않습니다. 문제를 방지하려면 다른 도메인 이름을 만들거나 아래각 서비스에 대해 제안된 이름을 따르십시오. 
 
Azure 서비스의 경우 다음 표에 설명된 대로 권장 영역 이름을 사용합니다.

|개인 링크 리소스 유형   |하위 리소스  |영역 이름  |
|---------|---------|---------|
|SQL DB(Microsoft.Sql/서버)    |  SQL 서버 (sqlServer)        |   privatelink.database.windows.net       |
|Azure 시냅스 분석(Microsoft.Sql/서버)    |  SQL 서버 (sqlServer)        | privatelink.database.windows.net |
|스토리지 계정(Microsoft.저장소/저장소 계정)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|스토리지 계정(Microsoft.저장소/저장소 계정)    |    표(표, table_secondary)      |   privatelink.table.core.windows.net       |
|스토리지 계정(Microsoft.저장소/저장소 계정)    |    큐(큐, queue_secondary)     |   privatelink.queue.core.windows.net       |
|스토리지 계정(Microsoft.저장소/저장소 계정)   |    파일(파일, file_secondary)      |    privatelink.file.core.windows.net      |
|스토리지 계정(Microsoft.저장소/저장소 계정)     |  웹(웹, web_secondary)        |    privatelink.web.core.windows.net      |
|데이터 레이크 파일 시스템 Gen2 (마이크로소프트.스토리지/스토리지계정)  |  데이터 레이크 파일 시스템 Gen2(dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure 코스모스 DB (마이크로소프트.AzureCosmosDB/데이터베이스계정)|SQL |privatelink.documents.azure.com|
|Azure 코스모스 DB (마이크로소프트.AzureCosmosDB/데이터베이스계정)|MongoDB |privatelink.mongo.cosmos.azure.com|
|Azure 코스모스 DB (마이크로소프트.AzureCosmosDB/데이터베이스계정)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure 코스모스 DB (마이크로소프트.AzureCosmosDB/데이터베이스계정)|Gremlin |privatelink.gremlin.cosmos.azure.com|
|Azure 코스모스 DB (마이크로소프트.AzureCosmosDB/데이터베이스계정)|테이블|privatelink.table.cosmos.azure.com|
|포스트 그레SQL에 대한 Azure 데이터베이스 - 단일 서버 (마이크로 소프트 DBforPostgreSQL / 서버)|포스트그레SQL서버|privatelink.postgres.database.azure.com|
|MySQL에 대 한 Azure 데이터베이스 (마이크로소프트.DBforMySQL/서버)|마이sql서버|privatelink.mysql.database.azure.com|
|마리아DB용 Azure 데이터베이스(Microsoft.DBforMariaDB/서버)|마리아드서버|privatelink.mariadb.database.azure.com|
|Azure 키 볼트(Microsoft.KeyVault/볼트)|자격 증명 모음|privatelink.vaultcore.azure.net|
|Azure Kubernetes 서비스 - Kubernetes API(Microsoft.ContainerService/managedCluster) | 관리되는 클러스터 | {guid}.개인 링크. <region>.azmk8s.io|
|Azure 검색(Microsoft.검색/검색 서비스)|검색 서비스|privatelink.search.windows.net|   
|Azure 컨테이너 레지스트리(Microsoft.컨테이너 레지스트리/레지스트리) | 사용된 | privatelink.azurecr.io |
|Azure 앱 구성(Microsoft.Appconfiguration/구성스토어)| 구성 스토어 | privatelink.azconfig.io|
|Azure 백업(Microsoft.RecoveryServices/볼트)| 자격 증명 모음 |개인 링크. {지역}.백업.windowsazure.com|
|Azure 이벤트 허브(Microsoft.EventHub/네임스페이스)| namespace |privatelink.servicebus.windows.net|
|Azure 서비스 버스(Microsoft.ServiceBus/네임스페이스) | namespace |privatelink.servicebus.windows.net|
|Azure 릴레이(Microsoft.릴레이/네임스페이스) | namespace |privatelink.servicebus.windows.net|
|Azure 이벤트 그리드(Microsoft.EventGrid/토픽)   | 토픽 | 항목. {지역}.프라이빗링크.eventgrid.azure.net|
|Azure 이벤트 그리드(Microsoft.EventGrid/도메인) | 도메인 | 도메인. {지역}.프라이빗링크.eventgrid.azure.net |
|Azure 웹앱스(Microsoft.웹/사이트) | site | privatelink.azurewebsites.net |
|Azure 기계 학습(Microsoft.MachineLearning서비스/작업 영역)   | 작업 영역 | privatelink.api.azureml.ms |
 
Azure는 공용 DNS에 표준 이름 DNS 레코드(CNAME)를 만들어 제안된 도메인 이름으로 해상도를 리디렉션합니다. 개인 끝점의 개인 IP 주소로 해상도를 재정의할 수 있습니다. 
 
응용 프로그램은 연결 URL을 변경할 필요가 없습니다. 공용 DNS를 사용하여 해결하려고 하면 DNS 서버가 이제 개인 끝점으로 확인됩니다. 이 프로세스는 응용 프로그램에 영향을 미치지 않습니다. 
 
## <a name="limitations"></a>제한 사항
 
다음 표에는 전용 끝점을 사용할 때 알려진 제한 사항 목록이 포함되어 있습니다. 


|제한 사항 |설명 |완화 방법  |
|---------|---------|---------|
|NSG(네트워크 보안 그룹) 규칙 및 사용자 정의 경로는 개인 엔드포인트에 적용되지 않습니다.    |NSG는 개인 끝점에서 지원되지 않습니다. 개인 끝점을 포함하는 서브넷에는 NSG가 연결될 수 있지만 규칙은 개인 끝점에서 처리되는 트래픽에 효과적이지 않습니다. 서브넷에 개인 끝점을 배포하려면 [네트워크 정책 적용을 사용하지 않도록 설정해야](disable-private-endpoint-network-policy.md) 합니다. NSG는 여전히 동일한 서브넷에서 호스팅되는 다른 워크로드에 적용됩니다. 모든 클라이언트 서브넷의 경로는 /32 접두사를 사용하므로 기본 라우팅 동작을 변경하려면 유사한 UDR이 필요합니다.  | 원본 클라이언트의 아웃바운드 트래픽에 NSG 규칙을 사용하여 트래픽을 제어합니다. 개인 끝점 경로를 재정의하기 위해 /32 접두사를 사용하여 개별 경로를 배포합니다. NSG 흐름 로그 및 아웃바운드 연결에 대한 모니터링 정보는 여전히 지원되며 사용할 수 있습니다.        |


## <a name="next-steps"></a>다음 단계
- [포털을 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-portal.md)
- [PowerShell을 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-powershell.md)
- [CLI를 사용하여 SQL Database Server용 프라이빗 엔드포인트 만들기](create-private-endpoint-cli.md)
- [포털을 사용하여 스토리지 계정용 프라이빗 엔드포인트 만들기](create-private-endpoint-storage-portal.md)
- [포털을 사용하여 Azure Cosmos 계정용 프라이빗 엔드포인트 만들기](../cosmos-db/how-to-configure-private-endpoints.md)
- [Azure PowerShell를 사용하여 고유의 Private Link 서비스 만들기](create-private-link-service-powershell.md)
- [PostgreSQL - 포털을 사용하여 단일 서버에 대한 Azure 데이터베이스에 대한 개인 링크 만들기](../postgresql/howto-configure-privatelink-portal.md)
- [PostgreSQL - CLI를 사용하여 단일 서버에 대한 Azure 데이터베이스에 대한 개인 링크 만들기](../postgresql/howto-configure-privatelink-cli.md)
- [포털을 사용하여 MySQL용 Azure 데이터베이스에 대한 개인 링크 만들기](../mysql/howto-configure-privatelink-portal.md)
- [CLI를 사용하여 MySQL용 Azure 데이터베이스에 대한 개인 링크 만들기](../mysql/howto-configure-privatelink-cli.md)
- [포털을 사용하여 MariaDB용 Azure 데이터베이스에 대한 개인 링크 만들기](../mariadb/howto-configure-privatelink-portal.md)
- [CLI를 사용하여 MariaDB용 Azure 데이터베이스에 대한 개인 링크 만들기](../mariadb/howto-configure-privatelink-cli.md)
