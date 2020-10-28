---
title: 데이터 액세스 전략
description: 이제 Azure Data Factory에서 고정 IP 주소 범위를 지원합니다.
services: data-factory
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 05/28/2020
ms.openlocfilehash: 785381e0a42f2b502e4ea7054753d5f3fb67f385
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92632773"
---
# <a name="data-access-strategies"></a>데이터 액세스 전략

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

조직의 중요한 보안 목표는 인터넷을 이용한 임의 액세스를 통해 온-프레미스 데이터 저장소 또는 클라우드/SaaS 데이터 저장소를 보호하는 데 있습니다. 

일반적으로 클라우드 데이터 저장소는 다음과 같은 메커니즘을 사용하여 액세스를 제어합니다.
* Virtual Network에서 개인 끝점을 사용 하는 데이터 원본에 대 한 개인 링크
* IP 주소를 통해 연결을 제한하는 방화벽 규칙
* 사용자에게 자신의 ID를 증명하도록 요구하는 인증 메커니즘
* 특정 작업 및 데이터로 사용자를 제한하는 권한 부여 메커니즘

> [!TIP]
> 이제 [고정 IP 주소 범위의 도입](./azure-integration-runtime-ip-addresses.md)을 통해 특정 Azure 통합 런타임 지역에 대해 목록 IP 범위를 허용할 수 있으며 클라우드 데이터 저장소에서 모든 Azure IP 주소를 허용할 필요가 없습니다. 이러한 식으로 데이터 저장소에 액세스할 수 있는 IP 주소를 제한할 수 있습니다.

> [!NOTE] 
> IP 주소 범위는 Azure Integration Runtime에 대해 차단 되며 현재 데이터 이동, 파이프라인 및 외부 활동에만 사용 됩니다. 이제 관리 Virtual Network를 사용 하도록 설정 하는 데이터 흐름 및 Azure Integration Runtime는 이러한 IP 범위를 사용 하지 않습니다. 

이는 다양한 시나리오에서 작동하며 통합 런타임당 고유한 고정 IP 주소를 사용하는 것이 바람직합니다. 다만 이제는 서버리스 Azure Integration Runtime을 사용해 이 작업을 수행할 수 없습니다. 필요하다면 자체 호스팅 통합 런타임을 항상 설정하고 그와 함께 고정 IP를 사용할 수 있습니다. 

## <a name="data-access-strategies-through-azure-data-factory"></a>Azure Data Factory를 통한 데이터 액세스 전략

* **[개인 링크](../private-link/private-link-overview.md)** -관리 되는 Azure Data Factory Virtual Network 내에 Azure Integration Runtime를 만들 수 있으며, 개인 끝점을 활용 하 여 지원 되는 데이터 저장소에 안전 하 게 연결 합니다. 관리 되는 Virtual Network와 데이터 원본 간의 트래픽은 Microsoft 백본 네트워크로 이동 하며 공용 네트워크에는 노출 되지 않습니다.
* **[신뢰할 수 있는 서비스](../storage/common/storage-network-security.md#exceptions)** - Azure Storage(Blob, ADLS Gen2)는 신뢰할 수 있는 Azure 플랫폼 서비스를 사용하여 스토리지 계정에 안전하게 액세스할 수 있는 방화벽 구성을 지원합니다. 신뢰할 수 있는 서비스는 관리 되는 Id 인증을 강제 적용 하 여 관리 되는 id를 사용 하도록 승인 하지 않는 한 다른 데이터 팩터리가이 저장소에 연결할 수 없도록 합니다. 자세한 내용은 **[이 블로그](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993)** 에서 확인할 수 있습니다. 따라서 이 방법은 매우 안전하며 권장됩니다. 
* **고유한 고정 IP** - Data Factory 커넥터에 대한 고정 IP를 가져오려면 자체 호스팅 통합 런타임을 설정해야 합니다. 이 메커니즘을 사용하면 다른 모든 IP 주소에서 액세스를 차단할 수 있습니다. 
* **[고정 IP 범위](./azure-integration-runtime-ip-addresses.md)** - Azure Integration Runtime의 IP 주소를 사용하여 스토리지(예를 들면 S3, Salesforce 등)에 이 범위를 나열할 수 있습니다. 또한 데이터 저장소에 연결할 수 있는 IP 주소를 제한할 뿐만 아니라 인증/권한 부여 규칙도 사용합니다.
* **[서비스 태그](../virtual-network/service-tags-overview.md)** - 서비스 태그는 (Azure Data Factory 같은) 지정된 Azure 서비스의 IP 주소 접두사 그룹을 나타냅니다. Microsoft는 서비스 태그에 포함되는 주소 접두사를 관리하고 주소가 변경되면 서비스 태그를 자동으로 업데이트하여 네트워크 보안 규칙을 자주 업데이트할 때 발생하는 복잡성을 최소화합니다. Virtual Network의 IaaS 호스트 된 데이터 저장소에서 데이터 액세스를 필터링 할 때 유용 합니다.
* **Azure 서비스 허용** - 이 옵션을 선택하는 경우, 일부 서비스를 사용하면 모든 Azure 서비스를 연결할 수 있습니다. 

Azure Integration Runtime 및 자체 호스팅 통합 런타임의 데이터 저장소에서 지원되는 네트워크 보안 메커니즘에 관한 자세한 내용은 다음 두 개의 표를 참조하세요.  
* **Azure Integration Runtime**

    | 데이터 저장소                  | 데이터 저장소에서 지원되는 네트워크 보안 메커니즘 | Private Link     | 신뢰할 수 있는 서비스     | 고정 IP 범위 | 서비스 태그 | Azure 서비스 허용 |
    |------------------------------|-------------------------------------------------------------|---------------------|-----------------|--------------|----------------------|-----------------|
    | Azure PaaS 데이터 저장소       | Azure Cosmos DB                                     | 예              | -                   | 예             | -            | 예                  |
    |                              | Azure Data Explorer                                 | -                | -                   | 예*            | 예*         | -                    |
    |                              | Azure Data Lake Gen1                                | -                | -                   | 예             | -            | 예                  |
    |                              | Azure Database for MariaDB, MySQL, PostgreSQL       | -                | -                   | 예             | -            | 예                  |
    |                              | Azure File Storage                                  | 예              | -                   | 예             | -            | .                    |
    |                              | Azure Storage (Blob, ADLS Gen2)                     | 예              | 예(MSI 인증만 해당) | 예             | -            | .                    |
    |                              | Azure SQL DB, Azure Synapse Analytics), SQL Ml  | 예 (Azure SQL DB/DW만 해당)        | -                   | 예             | -            | 예                  |
    |                              | Azure Key Vault(암호/연결 문자열을 가져오는 데 사용됨) | 예      | 예                 | 예             | -            | -                    |
    | 그 외 PaaS/SaaS 데이터 저장소 | AWS S3, SalesForce, Google Cloud Storage 등.    | -                | -                   | 예             | -            | -                    |
    | Azure laaS                   | SQL Server, Oracle 등                          | -                | -                   | 예             | 예          | -                    |
    | 온-프레미스 laaS              | SQL Server, Oracle 등                          | -                | -                   | 예             | -            | -                    |
    
    *Azure Data Explorer가 가상 네트워크에 삽입된 경우에만 적용되며, IP 범위는 NSG/방화벽에 적용될 수 있습니다. 

* **자체 호스팅 통합 런타임(Vnet 내부/온-프레미스)**
    
    | 데이터 저장소                  | 데이터 저장소에서 지원되는 네트워크 보안 메커니즘         | 고정 IP | 신뢰할 수 있는 서비스  |
    |--------------------------------|---------------------------------------------------------------|-----------|---------------------|
    | Azure PaaS 데이터 저장소       | Azure Cosmos DB                                               | 예       | -                   |
    |                                | Azure Data Explorer                                           | -         | -                   |
    |                                | Azure Data Lake Gen1                                          | 예       | -                   |
    |                                | Azure Database for MariaDB, MySQL, PostgreSQL               | 예       | -                   |
    |                                | Azure File Storage                                            | 예       | -                   |
    |                                | Azure Storage(블로그, ADLS Gen2)                             | 예       | 예(MSI 인증만 해당) |
    |                                | Azure SQL DB, Azure Synapse Analytics), SQL Ml          | 예       | -                   |
    |                                | Azure Key Vault(암호/연결 문자열을 가져오는 데 사용됨) | 예       | 예                 |
    | 그 외 PaaS/SaaS 데이터 저장소 | AWS S3, SalesForce, Google Cloud Storage 등.              | 예       | -                   |
    | Azure laaS                     | SQL Server, Oracle 등                                  | 예       | -                   |
    | 온-프레미스 laaS              | SQL Server, Oracle 등                                  | 예       | -                   |    

## <a name="next-steps"></a>다음 단계

자세한 내용은 다음 관련 문서를 참조하세요.
* [지원되는 데이터 저장소](./copy-activity-overview.md#supported-data-stores-and-formats)
* [Azure Key Vault의 ‘신뢰할 수 있는 서비스’](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)
* [Azure Storage의 ‘신뢰할 수 있는 Microsoft 서비스’](../storage/common/storage-network-security.md#trusted-microsoft-services)
* [Data Factory에 대한 관리 ID](./data-factory-service-identity.md)