---
title: Azure 프라이빗 엔드포인트 DNS 구성
description: Azure 프라이빗 엔드포인트 DNS 구성 알아보기
services: private-link
author: mblanco77
ms.service: private-link
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: allensu
ms.openlocfilehash: 7c8ff0808ada522dc24ef3c27156f4151832fbcd
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/20/2020
ms.locfileid: "83715939"
---
# <a name="azure-private-endpoint-dns-configuration"></a>Azure 프라이빗 엔드포인트 DNS 구성


연결 문자열의 일부로 FQDN(정규화된 도메인 이름)을 사용하여 프라이빗 링크 리소스에 연결하는 경우 할당된 프라이빗 IP 주소를 확인하도록 DNS 설정을 올바르게 구성하는 것이 중요합니다. 기존 Azure 서비스에는 공용 엔드포인트를 통해 연결할 때 사용할 DNS 구성이 이미 있을 수 있습니다. 이 구성은 프라이빗 엔드포인트를 사용하여 연결하도록 재정의해야 합니다. 
 
프라이빗 엔드포인트와 연결된 네트워크 인터페이스에는 지정된 프라이빗 링크 리소스에 대해 할당된 프라이빗 IP 주소 및 FQDN을 포함하여 DNS를 구성하는 데 필요한 전체 정보 집합이 포함되어 있습니다. 
 
다음 옵션을 사용하여 프라이빗 엔드포인트에 대한 DNS 설정을 구성할 수 있습니다. 
- **호스트 파일 사용(테스트에만 권장)** . 가상 머신의 호스트 파일을 사용하여 DNS를 재정의할 수 있습니다.  
- **프라이빗 DNS 영역 사용**. [프라이빗 DNS 영역](../dns/private-dns-privatednszone.md)을 사용하여 지정된 프라이빗 엔드포인트에 대한 DNS 확인을 재정의할 수 있습니다. 프라이빗 DNS 영역을 가상 네트워크에 연결하여 특정 도메인을 확인할 수 있습니다.
- **DNS 전달자 사용(옵션)** . DNS 전달자를 사용하여 지정된 프라이빗 링크 리소스에 대한 DNS 확인을 재정의할 수 있습니다. [DNS 서버](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)가 가상 네트워크에서 호스트되는 경우 프라이빗 DNS 영역을 사용하여 모든 프라이빗 링크 리소스에 대한 구성을 간소화하는 DNS 전달 규칙을 만들 수 있습니다.
 
> [!IMPORTANT]
> 공용 엔드포인트를 확인하는 데 적극적으로 사용되는 영역은 재정의하지 않는 것이 좋습니다. DNS를 공용 DNS로 전달하지 않으면 리소스에 대한 연결을 올바르게 확인할 수 없습니다. 문제를 방지하려면 서로 다른 도메인 이름을 만들거나 아래 각 서비스에 대해 제안된 이름을 따릅니다. 

## <a name="azure-services-dns-zone-configuration"></a>Azure 서비스 DNS 영역 구성
Azure 서비스는 공용 DNS에 CNAME(정식 이름 DNS 레코드)을 만들어 확인 사항을 제안된 프라이빗 도메인 이름으로 리디렉션합니다. 프라이빗 엔드포인트의 프라이빗 IP 주소를 사용하여 확인 사항을 재정의할 수 있습니다. 
 
애플리케이션에서 연결 URL을 변경할 필요가 없습니다. 공용 DNS를 사용하여 확인하려고 하면 이제 DNS 서버가 프라이빗 엔드포인트로 확인됩니다. 프로세스는 기존 애플리케이션에 영향을 주지 않습니다. 

Azure 서비스의 경우 다음 표에 설명된 대로 권장되는 영역 이름을 사용합니다.

| Private Link 리소스 종류 / 하위 리소스 |프라이빗 DNS 영역 이름 | 공용 DNS 영역 전달자 |
|---|---|---|---|
| SQL DB(Microsoft.Sql/servers) / Sql Server | privatelink.database.windows.net | database.windows.net |
| Azure Synapse Analytics(Microsoft.Sql/servers) / Sql Server  | privatelink.database.windows.net | database.windows.net |
| 스토리지 계정(Microsoft.Storage/storageAccounts) / Blob(blob, blob_secondary) | privatelink.blob.core.windows.net | blob.core.windows.net |
| 스토리지 계정(Microsoft Storage/storageAccounts) / 테이블(table, table_secondary) | privatelink.table.core.windows.net | table.core.windows.net |
| 스토리지 계정(Microsoft.Storage/storageAccounts) / 큐(queue, queue_secondary) | privatelink.queue.core.windows.net | queue.core.windows.net |
| 스토리지 계정(Microsoft.Storage/storageAccounts / 파일(file, file_secondary) | privatelink.file.core.windows.net | file.core.windows.net |
| 스토리지 계정(Microsoft.Storage/storageAccounts) / 웹(web, web_secondary)) | privatelink.web.core.windows.net | web.core.windows.net |
| 데이터 레이크 파일 시스템 Gen2(Microsoft.Storage/storageAccounts) / 데이터 레이크 파일 시스템 Gen2(dfs, dfs_secondary) | privatelink.dfs.core.windows.net | dfs.core.windows.net |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / SQL | privatelink.documents.azure.com | documents.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / MongoDB | privatelink.mongo.cosmos.azure.com | mongo.cosmos.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / Cassandra | privatelink.cassandra.cosmos.azure.com | cassandra.cosmos.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / Gremlin | privatelink.gremlin.cosmos.azure.com | gremlin.cosmos.azure.com |
| Azure Cosmos DB(Microsoft.AzureCosmosDB/databaseAccounts) / Table | privatelink.table.cosmos.azure.com | table.cosmos.azure.com |
| Azure Database for PostgreSQL - 단일 서버(Microsoft.DBforPostgreSQL/servers) / postgresqlServer | privatelink.postgres.database.azure.com | postgres.database.azure.com |
| Azure Database for MySQL(Microsoft.DBforMySQL/servers) / mysqlServer | privatelink.mysql.database.azure.com | mysql.database.azure.com |
| Azure Database for MariaDB(Microsoft.DBforMariaDB/servers) / mariadbServer | privatelink.mariadb.database.azure.com | mariadb.database.azure.com |
| Azure Key Vault(Microsoft.KeyVault/vaults) / 자격 증명 모음 | privatelink.vaultcore.azure.net | vault.azure.net <br> vaultcore.azure.net |
| Azure Kubernetes Service-Kubernetes API((Microsoft.ContainerService/managedClusters) / managedCluster | privatelink.{region}.azmk8s.io | {region}.azmk8s.io |
| Azure Search(Microsoft.Search/searchServices)/searchService | privatelink.search.windows.net | search.windows.net |
| Azure Container Registry(Microsoft.ContainerRegistry/registries) / 레지스트리 | privatelink.azurecr.io | azurecr.io |
| Azure App Configuration(Microsoft.AppConfiguration/configurationStores) / configurationStore | privatelink.azconfig.io | azconfig.io |
| Azure Backup(Microsoft.RecoveryServices/vaults) / 자격 증명 모음 | privatelink.{region}.backup.windowsazure.com | {region}.backup.windowsazure.com |
| Azure Event Hub(Microsoft.EventHub/namespaces) / 네임스페이스 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Service Bus(Microsoft.ServiceBus/namespaces) / 네임스페이스 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Relay(Microsoft.Relay/namespaces) / 네임스페이스 | privatelink.servicebus.windows.net | servicebus.windows.net |
| Azure Event Grid(Microsoft.EventGrid/topics) / 토픽 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure Event Grid(Microsoft.EventGrid/domains) / 도메인 | privatelink.eventgrid.azure.net | eventgrid.azure.net |
| Azure WebApps(Microsoft.Web/sites) / 사이트 | privatelink.azurewebsites.net | azurewebsites.net |
| Azure Machine Learning(Microsoft.MachineLearningServices/workspaces) / 작업 영역 | privatelink.api.azureml.ms | api.azureml.ms |

 


## <a name="dns-configuration-scenarios"></a>DNS 구성 시나리오

서비스의 FQDN은 공용 IP 주소에 자동으로 확인되므로 프라이빗 엔드포인트의 프라이빗 IP 주소로 확인하려면 DNS 구성을 적절하게 변경해야 합니다.

DNS는 프라이빗 엔드포인트 IP 주소를 적절한 방식으로 확인하여 애플리케이션이 제대로 작동하도록 하는 중요한 구성 요소입니다.

기본 설정에 따라 다음과 같은 시나리오를 DNS 확인에 통합할 수 있습니다.

- [사용자 지정 DNS 서버가 없는 가상 네트워크 워크로드](#virtual-network-workloads-without-custom-dns-server)
- [DNS 전달자를 사용하는 온-프레미스 워크로드](#on-premises-workloads-using-a-dns-forwarder)

## <a name="virtual-network-workloads-without-custom-dns-server"></a>사용자 지정 DNS 서버가 없는 가상 네트워크 작업

이 구성은 사용자 지정 DNS 서버가 없는 가상 네트워크 워크로드에 적합합니다. 이 시나리오에서 클라이언트는 프라이빗 엔드포인트 IP 주소를 Azure 제공 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)에 쿼리합니다. Azure DNS는 프라이빗 DNS 영역의 DNS 확인을 담당합니다.


> [!NOTE]
> 이 시나리오는 Azure SQL 데이터베이스 권장 프라이빗 DNS 영역을 사용합니다. 다른 서비스의 경우 다음 참조 [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration)을 사용하여 모델을 조정할 수 있습니다.

올바르게 구성하려면 다음 리소스가 필요합니다.

- 클라이언트 가상 네트워크

- 사설 DNS 영역 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)-[A 레코드 형식](../dns/dns-zones-records.md#record-types) 사용

- 프라이빗 엔드포인트 정보(FQDN 레코드 이름 및 프라이빗 IP 주소)

다음 다이어그램은 프라이빗 DNS 영역을 사용하는 가상 네트워크 워크로드의 DNS 확인 시퀀스를 보여 줍니다.

:::image type="content" source="media/private-endpoint-dns/single-vnet-azure-dns.png" alt-text="단일 가상 네트워크 및 Azure 제공 DNS":::

이 모델은 동일한 프라이빗 엔드포인트에 연결된 여러 피어링된 가상 네트워크로 확장될 수 있습니다. 모든 피어링된 가상 네트워크의 프라이빗 DNS 영역에 [새 가상 네트워크 링크를 추가](../dns/private-dns-virtual-network-links.md)하여 이 작업을 수행할 수 있습니다.

> [!IMPORTANT]
> 이 구성에는 단일 프라이빗 DNS 영역이 필요하며 다른 가상 네트워크에 대해 동일한 이름으로 여러 영역을 만들려면 DNS 레코드를 병합하는 작업을 수동으로 수행해야 합니다.

이 시나리오에서는 공통 프라이빗 엔드포인트를 공유하는 스포크 네트워크를 사용하는 [허브 & 스포크](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) 네트워킹 토폴로지가 있으며 모든 스포크 가상 네트워크는 동일한 프라이빗 DNS 영역에 연결됩니다. 

:::image type="content" source="media/private-endpoint-dns/hub-and-spoke-azure-dns.png" alt-text="Azure 제공 DNS를 사용하는 허브 및 스포크":::

## <a name="on-premises-workloads-using-a-dns-forwarder"></a>DNS 전달자를 사용하는 온-프레미스 워크로드

온-프레미스 워크로드에서 프라이빗 엔드포인트의 FQDN을 프라이빗 IP 주소로 확인할 수 있도록 하려면 DNS 전달자를 사용하여 Azure에 배포된 Azure 서비스 [공용 DNS 영역](#azure-services-dns-zone-configuration)을 확인해야 합니다.


다음 시나리오는 Azure에 DNS 전달자가 있는 온-프레미스 네트워크에 적합합니다 .이는 Azure에서 제공하는 DNS [168.63.129.16](../virtual-network/what-is-ip-address-168-63-129-16.md)에 대한 서버 수준 전달자를 통해 모든 DNS 쿼리를 확인하는 작업을 담당합니다. 

> [!NOTE]
> 이 시나리오는 Azure SQL 데이터베이스 권장 프라이빗 DNS 영역을 사용합니다. 다른 서비스의 경우 다음 참조 [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration)을 사용하여 모델을 조정할 수 있습니다.

올바르게 구성하려면 다음 리소스가 필요합니다.

- 온-프레미스 네트워크
- 가상 네트워크 [온-프레미스에 연결됨](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Azure에 배포된 DNS 전달자 
- 사설 DNS 영역 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md) - [A 레코드 형식](../dns/dns-zones-records.md#record-types) 사용
- 프라이빗 엔드포인트 정보(FQDN 레코드 이름 및 프라이빗 IP 주소)

다음 다이어그램은 [가상 네트워크에 연결된](../dns/private-dns-virtual-network-links.md) 프라이빗 DNS 영역에 의해 확인되는 Azure에 배포된 DNS 전달자를 사용하는 온-프레미스 네트워크의 DNS 확인 시퀀스를 보여 줍니다.

:::image type="content" source="media/private-endpoint-dns/on-premises-using-azure-dns.png" alt-text="Azure DNS를 사용하는 온-프레미스":::

이 구성은 이미 DNS 솔루션이 있는 온-프레미스 네트워크에 맞게 확장될 수 있습니다. 
온-프레미스 DNS 솔루션은 Azure에 배포된 DNS 전달자를 참조하는  [조건부 전달자](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)를 통해 Azure DNS에 DNS 트래픽을 전달하도록 구성해야 합니다.

> [!NOTE]
> 이 시나리오는 Azure SQL 데이터베이스 권장 프라이빗 DNS 영역을 사용합니다. 다른 서비스의 경우 다음 참조 [Azure 서비스 DNS 영역 구성](#azure-services-dns-zone-configuration)을 사용하여 모델을 조정할 수 있습니다.

올바르게 구성하려면 다음 리소스가 필요합니다.

- 사용자 지정 DNS 솔루션이 있는 온-프레미스 네트워크 
- 가상 네트워크 [온-프레미스에 연결됨](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/)
- Azure에 배포된 DNS 전달자
- 사설 DNS 영역 [privatelink.database.windows.net](../dns/private-dns-privatednszone.md)  - [A 레코드 형식](../dns/dns-zones-records.md#record-types) 사용
- 프라이빗 엔드포인트 정보(FQDN 레코드 이름 및 프라이빗 IP 주소)

다음 다이어그램은  [가상 네트워크에 연결된](../dns/private-dns-virtual-network-links.md) 프라이빗 DNS 영역에 의해 확인되는 Azure에 DNS 트래픽을 조건부로 전달하는 온-프레미스 네트워크의 DNS 확인 시퀀스를 보여 줍니다.

> [!IMPORTANT]
> 조건부 전달은  **privatelink**.database.windows.net 대신 권장 [**공용 DNS 영역 전달자**](#azure-services-dns-zone-configuration) (예: `database.windows.net`) 에 전달되어야 합니다.

:::image type="content" source="media/private-endpoint-dns/on-premises-forwarding-to-azure.png" alt-text="Azure DNS로 온-프레미스 전달":::


## <a name="next-steps"></a>다음 단계
- [프라이빗 엔드포인트에 대해 자세히 알아보기](private-endpoint-overview.md)
