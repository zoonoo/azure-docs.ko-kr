---
title: Azure Firewall 서비스 태그 개요
description: 이 문서는 Azure Firewall 서비스 태그의 개요입니다.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 2/25/2019
ms.author: victorh
ms.openlocfilehash: 1d03d896de947fcc938619c52a3690962a0d2d6c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/23/2019
ms.locfileid: "60193733"
---
# <a name="azure-firewall-service-tags"></a>Azure Firewall 서비스 태그

서비스 태그는 보안 규칙 생성에 대한 복잡성을 최소화할 수 있는 IP 주소 접두사의 그룹을 나타냅니다. 고유한 서비스 태그를 직접 만들거나 태그 내에 포함된 IP 주소를 지정할 수 없습니다. Microsoft에서는 서비스 태그에서 압축한 주소 접두사를 관리하고 주소를 변경하는 대로 서비스 태그를 자동으로 업데이트합니다.

Azure Firewall 서비스 태그는 네트워크 규칙 대상 필드에 사용할 수 있습니다. 특정 IP 주소 대신 서비스 태그를 사용할 수 있습니다.

## <a name="supported-service-tags"></a>지원되는 서비스 태그

Azure Firewall 네트워크 규칙에 사용할 수 있는 서비스 태그는 다음과 같습니다.

* **AzureCloud**(Resource Manager만 해당): 이 태그는 모든 [데이터 센터 공용 IP 주소](https://www.microsoft.com/download/details.aspx?id=41653)를 포함한 Azure에 대한 IP 주소 공간을 나타냅니다. 값으로 *AzureCloud*를 지정하는 경우 트래픽은 Azure 공용 IP 주소에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureCloud에 대한 액세스를 허용하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure AzureCloud에 액세스를 허용하려는 경우 *AzureCloud.EastUS*를 서비스 태그로 지정할 수 있습니다. 
* **AzureTrafficManager**(Resource Manager만 해당): 이 태그는 Azure Traffic Manager 프로브 IP 주소에 대한 IP 주소 공간을 나타냅니다. Traffic Manager 프로브 IP 주소에 대한 자세한 내용은 [Azure Traffic Manager FAQ](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)에서 찾을 수 있습니다. 
* **Storage**(Resource Manager만 해당): 이 태그는 Azure Storage 서비스의 IP 주소 공간을 나타냅니다. 값의 *저장소*를 지정하는 경우 트래픽은 저장소에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 저장소에 대한 액세스를 허용하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure Storage에 액세스를 허용하려는 경우 *Storage.EastUS*를 서비스 태그로 지정할 수 있습니다. 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 Azure Storage 계정이 아닌 Azure Storage 서비스를 나타냅니다.
* **Sql**(Resource Manager만 해당): 이 태그는 Azure SQL Database 및 Azure SQL Data Warehouse 서비스의 주소 접두사를 나타냅니다. 값의 *Sql*을 지정하는 경우 트래픽은 Sql에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 Sql에 대한 액세스를 허용하려는 경우 지역을 지정할 수 있습니다. 예를 들어 미국 동부 지역에서만 Azure SQL Database에 액세스를 허용하려는 경우 *Sql.EastUS*를 서비스 태그로 지정할 수 있습니다. 태그는 서비스의 특정 인스턴스가 아니라 서비스를 나타냅니다. 예를 들어 태그는 특정 SQL 데이터베이스 또는 서버가 아닌 Azure SQL Database 서비스를 나타냅니다.
* **AzureCosmosDB**(Resource Manager만 해당): 이 태그는 Azure Cosmos Database 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureCosmosDB*를 지정하는 경우 트래픽은 AzureCosmosDB에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureCosmosDB 액세스를 허용하려면 AzureCosmosDB.[지역 이름] 형식으로 지역을 지정하면 됩니다.
* **AzureKeyVault**(Resource Manager만 해당): 이 태그는 Azure KeyVault 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureKeyVault*를 지정하는 경우 트래픽은 AzureKeyVault에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureKeyVault 액세스를 허용하려면 AzureKeyVault.[지역 이름] 형식으로 지역을 지정하면 됩니다.
* **EventHub**(Resource Manager만 해당): 이 태그는 Azure EventHub 서비스의 주소 접두사를 나타냅니다. 값으로 *EventHub*를 지정하는 경우 트래픽은 EventHub에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 EventHub 액세스를 허용하려면 EventHub.[지역 이름] 형식으로 지역을 지정하면 됩니다. 
* **ServiceBus**(Resource Manager만 해당): 이 태그는 Azure ServiceBus 서비스의 주소 접두사를 나타냅니다. 값으로 *ServiceBus*를 지정하는 경우 트래픽은 ServiceBus에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 ServiceBus 액세스를 허용하려면 ServiceBus.[지역 이름] 형식으로 지역을 지정하면 됩니다.
* **MicrosoftContainerRegistry**(Resource Manager만 해당): 이 태그는 Microsoft Container Registry 서비스의 주소 접두사를 나타냅니다. 값으로 *MicrosoftContainerRegistry*를 지정하는 경우 트래픽은 MicrosoftContainerRegistry에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 MicrosoftContainerRegistry 액세스를 허용하려면 MicrosoftContainerRegistry.[지역 이름] 형식으로 지역을 지정하면 됩니다.
* **AzureContainerRegistry**(Resource Manager만 해당): 이 태그는 Azure Container Registry 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureContainerRegistry*를 지정하는 경우 트래픽은 AzureContainerRegistry에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureContainerRegistry 액세스를 허용하려면 AzureContainerRegistry.[지역 이름] 형식으로 지역을 지정하면 됩니다. 
* **AppService**(Resource Manager만 해당): 이 태그는 Azure AppService 서비스의 주소 접두사를 나타냅니다. 값으로 *AppService*를 지정하는 경우 트래픽은 AppService에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AppService 액세스를 허용하려면 AppService.[지역 이름] 형식으로 지역을 지정하면 됩니다. 
* **AppServiceManagement**(Resource Manager만 해당): 이 태그는 Azure AppService Management 서비스의 주소 접두사를 나타냅니다. 값으로 *AppServiceManagement*를 지정하는 경우 트래픽은 AppServiceManagement에 대해 허용되거나 거부됩니다. 
* **ApiManagement**(Resource Manager만 해당): 이 태그는 Azure API Management 서비스의 주소 접두사를 나타냅니다. 값으로 *ApiManagement*를 지정하는 경우 트래픽은 ApiManagement에 대해 허용되거나 거부됩니다.  
* **AzureConnectors**(Resource Manager만 해당): 이 태그는 Azure Connectors 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureConnectors*를 지정하는 경우 트래픽은 AzureConnectors에 대해 허용되거나 거부됩니다. 특정 [지역](https://azure.microsoft.com/regions)에서만 AzureConnectors 액세스를 허용하려면 AzureConnectors.[지역 이름] 형식으로 지역을 지정하면 됩니다.
* **AzureDataLake**(Resource Manager만 해당): 이 태그는 Azure Data Lake 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureDataLake*를 지정하는 경우 트래픽은 AzureDataLake에 대해 허용되거나 거부됩니다.
* **AzureActiveDirectory**(Resource Manager만 해당): 이 태그는 AzureActiveDirectory 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureActiveDirectory*를 지정하는 경우 트래픽은 AzureActiveDirectory에 대해 허용되거나 거부됩니다.
* **AzureMonitor**(Resource Manager만 해당): 이 태그는 AzureMonitor 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureMonitor*를 지정하는 경우 AzureMonitor에 대한 트래픽이 허용 또는 거부됩니다.
* **ServiceFabric**(Resource Manager만 해당): 이 태그는 ServiceFabric 서비스의 주소 접두사를 나타냅니다. 값으로 *ServiceFabric*을 지정하는 경우 ServiceFabric에 대한 트래픽이 허용 또는 거부됩니다.
* **AzureMachineLearning**(Resource Manager만 해당): 이 태그는 AzureMachineLearning 서비스의 주소 접두사를 나타냅니다. 값으로 *AzureMachineLearning*를 지정하는 경우 AzureMachineLearning에 대한 트래픽이 허용 또는 거부됩니다.

## <a name="next-steps"></a>다음 단계

Azure Firewall 규칙에 대한 자세한 내용은 [Azure Firewall 규칙 처리 논리](rule-processing.md)를 참조하세요.