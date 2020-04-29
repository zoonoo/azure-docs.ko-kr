---
title: Azure 개인 끝점 DNS 구성
description: Azure 개인 끝점 DNS 구성 알아보기
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 477a5ffa971120d1a98c09ac4ae8ebda1c82b770
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/28/2020
ms.locfileid: "82209029"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 개인 끝점 DNS 구성


FQDN (정규화 된 도메인 이름)을 사용 하 여 연결 문자열의 일부로 개인 링크 리소스에 연결 하는 경우 할당 된 개인 IP 주소를 확인 하도록 DNS 설정을 올바르게 구성 하는 것이 중요 합니다. 기존 Azure 서비스에는 공용 끝점을 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. 개인 끝점을 사용 하 여 연결 하려면이를 재정의 해야 합니다. 
 
개인 끝점과 연결 된 네트워크 인터페이스에는 지정 된 개인 링크 리소스에 대해 할당 된 FQDN 및 개인 IP 주소를 포함 하 여 DNS를 구성 하는 데 필요한 전체 정보 집합이 포함 되어 있습니다. 
 
다음 옵션을 사용 하 여 개인 끝점에 대 한 DNS 설정을 구성할 수 있습니다. 
- **호스트 파일 (테스트에만 권장 됨)을 사용**합니다. 가상 컴퓨터의 호스트 파일을 사용 하 여 DNS를 재정의할 수 있습니다.  
- **개인 DNS 영역을 사용**합니다. [개인 dns 영역](../dns/private-dns-privatednszone.md) 을 사용 하 여 지정 된 개인 끝점에 대 한 DNS 확인을 재정의할 수 있습니다. 개인 DNS 영역을 가상 네트워크에 연결 하 여 특정 도메인을 확인할 수 있습니다.
- **사용자 지정 DNS 서버를 사용**합니다. 자체 DNS 서버를 사용 하 여 지정 된 개인 링크 리소스에 대 한 DNS 확인을 재정의할 수 있습니다. [Dns 서버가](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server) 가상 네트워크에서 호스트 되는 경우 개인 dns 영역을 사용 하 여 모든 개인 링크 리소스에 대 한 구성을 간소화 하는 dns 전달 규칙을 만들 수 있습니다.
 
> [!IMPORTANT]
> 공용 끝점을 확인 하는 데 적극적으로 사용 되는 영역은 재정의 하지 않는 것이 좋습니다. 공용 DNS로 DNS를 전달 하지 않고 리소스에 대 한 연결을 올바르게 확인할 수 없습니다. 문제를 방지 하려면 다른 도메인 이름을 만들거나 아래 각 서비스에 대 한 제안 된 이름을 따르세요. 

## <a name="azure-services-dns-zone-configuration"></a>Azure 서비스 DNS 영역 구성
Azure 서비스는 공용 DNS에서 정식 이름 DNS 레코드 (CNAME)를 만들어 확인을 제안 된 개인 도메인 이름으로 리디렉션합니다. 개인 끝점의 개인 IP 주소를 사용 하 여 해상도를 재정의할 수 있습니다. 
 
응용 프로그램에서 연결 URL을 변경할 필요가 없습니다. 공용 DNS를 사용 하 여 확인 하려고 하면 DNS 서버가 개인 끝점으로 확인 됩니다. 이 프로세스는 기존 응용 프로그램에 영향을 주지 않습니다. 

Azure 서비스의 경우 다음 표에 설명 된 대로 권장 된 영역 이름을 사용 합니다.

|개인 링크 리소스 유형   |Subresource  |영역 이름  |
|---------|---------|---------|
|SQL DB (Microsoft .Sql/servers)    |  Sql Server (sqlServer)        |   privatelink.database.windows.net       |
|Azure Synapse Analytics (Microsoft .Sql/servers)    |  Sql Server (sqlServer)        | privatelink.database.windows.net |
|저장소 계정 (Microsoft. Storage/storageAccounts)    |  Blob (blob, blob_secondary)        |    privatelink.blob.core.windows.net      |
|저장소 계정 (Microsoft. Storage/storageAccounts)    |    테이블 (테이블, table_secondary)      |   privatelink.table.core.windows.net       |
|저장소 계정 (Microsoft. Storage/storageAccounts)    |    큐 (큐, queue_secondary)     |   privatelink.queue.core.windows.net       |
|저장소 계정 (Microsoft. Storage/storageAccounts)   |    파일 (파일, file_secondary)      |    privatelink.file.core.windows.net      |
|저장소 계정 (Microsoft. Storage/storageAccounts)     |  웹 (웹, web_secondary)        |    privatelink.web.core.windows.net      |
|Data Lake 파일 시스템 Gen2 (Microsoft Storage/storageAccounts)  |  Data Lake 파일 시스템 Gen2 (dfs, dfs_secondary)        |     privatelink.dfs.core.windows.net     |
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|SQL    |privatelink.documents.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|MongoDB    |privatelink.mongo.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|Cassandra|privatelink.cassandra.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|Gremlin    |privatelink.gremlin.cosmos.azure.com|
|Azure Cosmos DB (Microsoft.azurecosmosdb/databaseAccounts)|테이블|privatelink.table.cosmos.azure.com|
|Azure Database for PostgreSQL-단일 서버 (DBforPostgreSQL/servers)|postgresqlServer|privatelink.postgres.database.azure.com|
|Azure Database for MySQL (Microsoft DBforMySQL/servers)|mysqlServer|privatelink.mysql.database.azure.com|
|Azure Database for MariaDB (DBforMariaDB/servers)|mariadbServer|privatelink.mariadb.database.azure.com|
|Azure Key Vault (Microsoft. KeyVault/자격 증명 모음)|자격 증명 모음|privatelink.vaultcore.azure.net|
|Azure Kubernetes Service-Kubernetes API (ContainerService/managedClusters)    | managedCluster | {guid}. privatelink. {region}. azmk8s|
|Azure Search (Microsoft 검색/searchServices)|searchService|privatelink.search.windows.net|   
|Azure Container Registry (Microsoft.containerregistry/레지스트리) | 사용된 | privatelink.azurecr.io |
|Azure 앱 구성 (Microsoft Appconfiguration/configurationStores)| configurationStore | privatelink.azconfig.io|
|Azure Backup (Microsoft RecoveryServices/자격 증명 모음)| 자격 증명 모음 |privatelink. {region}. windowsazure.servicebus|
|Azure 이벤트 허브 (Microsoft EventHub/네임 스페이스)| namespace |privatelink.servicebus.windows.net|
|Azure Service Bus (ServiceBus/네임 스페이스) | namespace |privatelink.servicebus.windows.net|
|Azure Relay (Microsoft Relay/네임 스페이스) | namespace |privatelink.servicebus.windows.net|
|Azure Event Grid (Microsoft EventGrid/토픽)     | 토픽 | 뒷부분. {region}. privatelink|
|Azure Event Grid (Microsoft EventGrid/도메인) | 도메인 | 도메인. {region}. privatelink |
|Azure WebApps (Microsoft 웹 사이트)    | site | privatelink.azurewebsites.net |
|Azure Machine Learning (MachineLearningServices/작업 영역)    | 작업 영역 | privatelink.api.azureml.ms |
 


## <a name="dns-configuration-scenarios"></a>DNS 구성 시나리오

서비스의 FQDN은 공용 ip 주소를 확인 하 고, 개인 끝점의 개인 IP 주소를 확인 하려면 DNS 구성을 변경 해야 합니다.

DNS는 개인 끝점 IP 주소를 적절 한 방식으로 확인 하 여 응용 프로그램이 제대로 작동 하도록 하는 중요 한 구성 요소입니다.

기본 설정에 따라 다음과 같은 시나리오를 DNS 확인에 통합할 수 있습니다.

- [사용자 지정 DNS 서버가 없는 작업 Virtual Network](#virtual-network-workloads-without-custom-dns-server)


## <a name="virtual-network-workloads-without-custom-dns-server"></a>사용자 지정 DNS 서버가 없는 작업 Virtual Network

이 구성은 사용자 지정 DNS 서버가 없는 가상 네트워크 작업에 적합 합니다. 이 시나리오에서 클라이언트는 Azure에 제공 된 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)개인 끝점 IP 주소를 쿼리 합니다. Azure DNS은 개인 DNS 영역에 대 한 DNS 확인을 담당 합니다.


 > [!NOTE]
> 이 시나리오에서는 Azure SQL database 권장 사설 DNS 영역을 사용 합니다. 다른 서비스의 경우 다음 [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration)참조를 사용 하 여 모델을 조정할 수 있습니다.

올바르게 구성 하려면 다음 리소스가 필요 합니다.

- 클라이언트 가상 네트워크

- [유형 A 레코드](../dns/dns-zones-records.md#record-types) 를 사용 하 여 영역 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) 사설 DNS

- 개인 끝점 정보 (FQDN 레코드 이름 및 개인 IP 주소)

다음 다이어그램은 개인 dns 영역을 사용 하는 가상 네트워크 워크 로드의 DNS 확인 시퀀스를 보여 줍니다.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="단일 가상 네트워크 및 azure에서 제공 하는 dns":::

이 모델은 동일한 개인 끝점에 연결 된 여러 피어 링 가상 네트워크로 확장 될 수 있습니다. 모든 피어 링 가상 네트워크의 개인 DNS 영역에 [새 가상 네트워크 링크를 추가](../dns/private-dns-virtual-network-links.md) 하 여이 작업을 수행할 수 있습니다.

 > [!IMPORTANT]
>  이 구성에는 단일 개인 DNS 영역이 필요 합니다. 다른 가상 네트워크에 대해 동일한 이름으로 여러 영역을 만들려면 DNS 레코드를 병합 하는 작업을 수동으로 수행 해야 합니다.

이 시나리오에서는 공통 개인 끝점을 공유 하는 스포크 네트워크를 사용 하는 [허브 & 스포크](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 네트워킹 토폴로지 및 모든 스포크 가상 네트워크가 동일한 개인 dns 영역에 연결 되어 있습니다. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="azure에서 제공 하는 dns를 사용 하는 허브 및 스포크":::


## <a name="next-steps"></a>다음 단계
- [개인 끝점에 대 한 자세한 정보](private-endpoint-overview.md)
