---
title: SQL Database용 Azure Resource Manager 템플릿 | Microsoft Docs
description: Azure Resource Manager 템플릿을 사용하여 Azure SQL Database를 만들고 구성합니다.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: b967dc872529ec8b045df81542eec4c555b17a6c
ms.sourcegitcommit: c05618a257787af6f9a2751c549c9a3634832c90
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/30/2019
ms.locfileid: "66418442"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Azure SQL Database용 Azure Resource Manager 템플릿

Azure Resource Manager 템플릿을 사용하여 인프라를 코드로 정의하고 Azure 클라우드에 솔루션을 배포할 수 있습니다.

## <a name="single-database--elastic-pool"></a>단일 데이터베이스 및 탄력적 풀

다음 표에는 Azure SQL Database용 Azure Resource Manager 템플릿 링크가 포함되어 있습니다.

| |  |
|---|---|
| [단일 데이터베이스](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | 이 Azure Resource Manager 템플릿은 논리 서버를 사용하여 단일 Azure SQL Database를 만들고 방화벽 규칙을 구성합니다. |
| [논리 서버](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | 이 Azure Resource Manager 템플릿은 Azure SQL Database에 대한 논리 서버를 만듭니다. |
| [탄력적 풀](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | 이 템플릿을 통해 새 탄력적 풀 그리고 풀과 연결된 새 SQL Server 및 새 SQL Database를 배포하여 할당할 수 있습니다. |
| [장애 조치(failover) 그룹](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | 이 템플릿은 두 개의 Azure SQL 논리 서버와 SQL 데이터베이스 및 장애 조치(failover) 그룹을 만듭니다.|
| [위협 탐지](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | 이 템플릿을 통해 각 데이터베이스의 경고 이메일 주소를 사용하여 Azure SQL 논리 서버 그리고 위협 탐지가 설정된 Azure SQL Database 세트를 배포할 수 있습니다. 위협 탐지는 SQL ATP(Advanced Threat Protection) 제품의 일부이며 SQL 서버 및 데이터베이스에 대한 잠재적 위협에 대응하는 보안 레이어를 제공합니다.|
| [Azure Blob Storage 감사](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | 이 템플릿을 통해 Blob 스토리지에 감사 로그를 쓰도록 설정된 Azure SQL 논리 서버를 배포할 수 있습니다. Azure SQL Database 감사는 데이터베이스 이벤트를 추적하여 Azure 스토리지 계정, OMS 작업 영역 또는 Event Hubs에 배치할 수 있는 감사 로그에 기록합니다.|
| [Azure Event Hub 감사](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | 이 템플릿을 통해 기존 Event Hub에 감사 로그를 쓰도록 설정된 Azure SQL 서버를 배포할 수 있습니다. Event Hub에 감사 이벤트를 보내려면 감사 설정을 `Enabled` `State`로 지정하고 `IsAzureMonitorTargetEnabled`를 `true`로 설정합니다. 또한 `master` 데이터베이스에서 `SQLSecurityAuditEvents` 진단 로그 범주를 사용하여 진단 설정을 구성합니다(서버 수준 감사인 경우). Azure SQL Database 및 SQL Data Warehouse 감사는 데이터베이스 이벤트를 추적하여 Azure 스토리지 계정, OMS 작업 영역 또는 Event Hubs에 배치할 수 있는 감사 로그에 기록합니다.|
| [SQL Database를 사용하는 Azure 웹앱](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | 이 샘플은 "기본" 서비스 수준에서 체험판 Azure 웹앱과 SQL Database를 만듭니다.|
| [SQL Database를 사용하는 Azure 웹앱 및 Redis Cache](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | 이 템플릿은 동일한 리소스 그룹에 웹앱, Redis Cache 및 SQL Database를 만들고, 웹앱에 SQL Database 및 Redis Cache에 대한 두 개의 연결 문자열을 만듭니다.|
| [ADF V2를 사용하여 Blob 스토리지에서 데이터 가져오기](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | 이 Azure Resource Manager 템플릿은 Azure Blob Storage에서 SQL Database로 데이터를 복사하는 Azure Data Factory V2를 만듭니다.|
| [SQL Database를 사용하는 HDInsight 클러스터](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | 이 템플릿을 사용하면 HDInsight 클러스터, SQL Database 서버, SQL Database 및 두 개의 테이블을 만들 수 있습니다. 이 템플릿은 [HDInsight에서 Hadoop과 Sqoop 사용](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) 문서에서 사용됩니다. |
| [일정에 따라 SQL 저장 프로시저를 실행하는 Azure 논리 앱](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | 이 템플릿을 사용하면 일정에 따라 SQL 저장 프로시저를 실행하는 논리 앱을 만들 수 있습니다. 프로시저에 대한 인수를 템플릿의 본문 섹션에 삽입할 수 있습니다.|

## <a name="managed-instance"></a>관리되는 인스턴스

다음 표에는 Azure SQL Database - Managed Instance에 대한 Azure Resource Manager 템플릿 링크가 포함되어 있습니다.

| |  |
|---|---|
| [새 VNet의 Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | 이 Azure Resource Manager 템플릿은 VNet에 새로 구성된 Azure VNet 및 Managed Instance를 만듭니다. |
| [Managed Instance에 대한 네트워크 환경](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | 이 배포는 두 개의 서브넷이 있는 구성된 Azure Virtual Network를 만듭니다. 두 서브넷 중 하나는 Managed Instance 전용이고, 다른 하나에는 기타 리소스(예: VM, App Service 환경 등)를 배치할 수 있습니다. 이 템플릿은 Managed Instance를 배포할 수 있도록 올바르게 구성된 네트워킹 환경을 만듭니다. |
| [P2S 연결을 사용하는 Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | 이 배포는 `ManagedInstance` 및 `GatewaySubnet` 서브넷이 있는 Azure Virtual Network를 만듭니다. Managed Instance는 ManagedInstance 서브넷에 배포됩니다. 가상 네트워크 게이트웨이는 `GatewaySubnet` 서브넷에 생성되고 지점-사이트 간 VPN 연결에 대해 구성됩니다. |
| [가상 머신을 사용하는 Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | 이 배포는 `ManagedInstance` 및 `Management` 서브넷이 있는 Azure Virtual Network를 만듭니다. Managed Instance는 `ManagedInstance` 서브넷에 배포됩니다. 최신 버전의 SSMS(SQL Server Management Studio)가 설치된 가상 머신이 `Management` 서브넷에 배포됩니다. |
